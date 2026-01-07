---
title: Go Programming Languages - Extra Settings
tags: [Go]
categories: Programming Languages
cover: images/ProgrammingLanguages/Go/cover.svg
excerpt: Go需要知道的一些配置信息
---

# Go module

## Go module的原理
go module 根据官网定义

> A Go module is a collection of Go packages stored in a file tree with a go.mod file at its root

在Go Module没有出现之前，所有依赖距均从`$GOPATH/src/` 或 `$GOROOT/src/` 中进行查找导入，Go Modules 是为了提升使用其他开发者代码，即添加**依赖项（模块、包）**时的体验，也是为了让代码的正确性、安全性得到保障。

在 Go 支持 Go Modules 之后，编译时编译器会从工作目录（当前所在目录）开始并逐级向上查找是否具有 go.mod 文件。

## 如何开启？
用环境变量 GO111MODULE 开启或关闭模块支持，它有三个可选值：off、on、auto，默认值是 auto。

- `GO111MODULE=off` 无模块支持，Go 会从 GOPATH 和 vendor 文件夹寻找包。
- `GO111MODULE=on` 模块支持，Go 会忽略 GOPATH 和 vendor 文件夹，只根据 go.mod 下载依赖。
- `GO111MODULE=auto` 在 `$GOPATH/src` 外面且根目录有 go.mod 文件时，开启模块支持。 

注意：在使用模块的时候，GOPATH 是无意义的，不过它还是会把下载的依赖储存在 `$GOPATH/src/mod` 中，也会把 go install 的结果放在 `$GOPATH/bin` 中

## 如何初始化一个module
```shell
go mod init ${module_name}
```

## Go module 名称
module名称有模块路径标识，模块路径就是模块的规范名称，为了规范，很多公司会将确保模块名称加上`https://`的前缀就是源码仓库的地址。

举例：
![module名称与实际github地址相同](/images/ProgrammingLanguages/Go/go_extra/module.png)

这样做的目的是为了方便其他公司/组织的人下载依赖时使用

注意：
1. 模块名可以不是源码仓库地址
2. 模块名称可以包含子目录

下面讨论如何下载，已经如果模块名称不为实际下载地址的时候Go module是如何解决的。

## 如何下载Go Module（下载自己项目的依赖项）？
一般来说，只需要知道你需要下载的module名称即可，比如我需要下载上图testify的包，我只需要：

```shell
go get github.com/stretchr/testify
```

但有的时候，一些module的名称与源码仓库地址不一样，比如下图：
![module名称与实际github地址不相同](/images/ProgrammingLanguages/Go/go_extra/module2.png)

当执行get命令的时候：
```shell
go get go.uber.org/zap
```

会先向https://go.uber.org/zap?go-get=1发送一个`GET`请求
![postman显示发送get请求后的响应体](/images/ProgrammingLanguages/Go/go_extra/module3.png)


此时，响应体里会有一个meta标签，name为`go-import`，对应的content由三部分组成：

```
# 1. 模块名称
# 2. 版本控制工具（git还是svn）
# 3. 实际的源码仓库地址
go.uber.org/zap git https://github.com/uber-go/zap
```

## 如何下载模块中的子模块（下载自己项目的依赖项中的其中一个模块）？
假设我需要的不是`zap`这个项目的所有源码，而是其中一个模块，比如
```shell
go get go.uber.org/zap/tools
```
他的查找顺序如下：
1. 先发送`GET`请求`https://go.uber.org/zap/tools?go-get=1`，发现查不到源码地址
2. 再回溯一级目录，请求`https://go.uber/org/zap?go-get=1`

## 公司里面的做法

1. 公司里面一般我们会直接`go get xxxx/repo.git`，这个时候就会直接克隆这个git地址的目录，无需再发送`go-get=1`的请求
2. 一般来说，公司里面实际上会使用ssh的方式解决权限问题，比如使用git的时候我们会在$HOME/.gitconfig文件中追加
```
[url "ssh://git@xxx.com"]
    insteadOf = https://xxx.com
```
通过在git平台上传ssh公钥的方式解决权限问题

# GOPROXY

## GOPROXY原理
几个问题：
1. 互联网上的源码仓库除了github还有很多，在各种不同的网络环境下，如何保证下载速度？
2. 默认情况下，go get不是直接去源码仓库上下载代码，而是通过代理`https://proxy.golang.org`(谷歌维护)去下载，代理对源码镜像进行了缓存并提供了CDN加速，但是源码更新几分钟后，代理里的镜像才会更新会有一定延迟。

国内代理有：
```
https://goproxy.cn
https://goproxy.io
```

我们可以在GOPROXY环境变量下进行更改，以确保当谷歌的下载不下来的时候，可以使用国内代理下载。

## 模块缓存
下载的谋爱会保存在环境变量`GOMODCACHE`指定的目录下，默认为`$GOPATH/pkg/mod`

## GOSUMDB
sum.golang.org (由谷歌运行)提供了一个checksum数据库，用来存储源代码的哈希值，以防止go get从任何源头（包括代理）拉取了被篡改的源码。

1. 第一次用go get下载某个模块的时候，会计算其哈希值，与checksum数据库里的值进行对比，
2. 如果一致，则把模块存入本地缓存目录，并将哈希值写入go.sum文件。后续使用该模块时通过go.sum文件来校验该模块自下载以来未曾被修改过。
3. 如果设置环境变量`GOSUMDB=off`，或者go get的时候使用-insecure标志，标识不需要验证合法性。

## 如何使用公司内部私有的Go module

1. GOPROXY的默认值（`https://proxy.golang.org,direct`），其中direct表示不走代理，直接从源码库下载（先发送go-get=1请求）
2. 所有代理都不会访问私有仓库，所以go get私有模块的时候会命中direct，所有代理都不会访问私有仓库，所以go get私有模块的时候会命中direct。为避免下载私有模块的时候访问代理，可以把私有模块的前缀赋给`GONOPROXY`，如`GONOPROXY=xxx.xxx.com`
3. 私有模块不需要使用公共的checksum数据库，所以需要设置`GONOSUMDB=xxx.xxx.com`
4. GOPRIVATE是`GONOPROXY`和`GONOSUMDB`的默认值，所以设置GOPRIVATE之后就不需要再设置`GONOPROXY`和`GONOSUMDB`

## 如何部署公司私有的GOPROXY

`goproxy.io`除了提供国内可用的go module代理之外，还提供了部署私有go proxy的能力：

[https://github.com/goproxyio/goproxy.git](https://github.com/goproxyio/goproxy.git)

方法如下：

1. 编译安装go proxy
```shell
git clone https://github.com/goproxyio/goproxy.git
cd goproxy
make
```

2. 启动代码
```shell
./bin/goproxy -listen=0.0.0.0:80 -cacheDir=$HOME/go_module_cache -proxy https://goproxy.io -exclude "gitlab.xxx.com"
```

- 其中 `-cacheDir` 是代理使用的缓存目录，跟GOMODCACHE区分开，如果私有代理商找不到模块，就去访问公开代理（也就是https://goproxy.io）。
- `-proxy`指定公开代理
- `-exclude`指定那些模块直接去代码仓库下载
- 最后将自己的GOPROXY设置为`GOPROXY=xxx.com,direct`，xxx.com就是你服务器的域名，80端口开放

