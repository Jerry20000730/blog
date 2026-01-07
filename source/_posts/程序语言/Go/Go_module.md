---
title: Go Programming Languages - Go Module Version
tags: [Go]
categories: Programming Languages
cover: images/ProgrammingLanguages/Go/cover.svg
excerpt: Go版本规范
---

# Go Module 版本规范

1. Go module 每个版本以v开头，后面跟_语义版本_。当vcs为git时候，通常情况下模块的版本会是git tag的版本

![go module的版本号](/images/ProgrammingLanguages/Go/go_module/module_version.png)
![git tag版本](/images/ProgrammingLanguages/Go/go_module/git_version.png)

2. 当没有语义版本的时候，会生成一个伪版本，例如`v0.0.0-20190306012644-bacd9c7ef1dd`这种，其中第二部分代表代码生成的时间，第三部分是这个代码当前commit的id的前12个字符。（可以通过`git log`来查看）

3. 主版本为2或更高版本的时候，go模块路径必须带有类似于`/v2`或`/v3`这样的主版本后缀，比如`github.com/gocolly/colly/v2`，`github.com/mailru/go-clickhouse/v2`，这就是为什么`go get`或`go install`的时候可以指定版本号，如：

```shell
go get github.com/cespare/xxhash@v1.1.0
```

4. 主版本号不同表示不兼容，一个项目里可能同时依赖不同的主版本号，比如

```go
import (
    "gopkg.in/yaml.v2"
    yaml3 "gopkg.in/yaml.v3"
)
```

5. `go get -u`不会更新主版本号，即`-u`表示更新到当前主版本号下的最新版本

6. 在使用go module规范之前，有些第三方库的高版本没有加`/v2`或`/v3`后缀，此时需要加incompatible，如`github/go-redis/redis v6.15.9+incompatible`路径没有`/v6`后缀

7. 主版本后缀不允许有`/v0`或`/v1`出现
    a. 一种特殊情况：以`gopkg.in/`开头的模块路径必须始终具有主版本后缀。后缀必须以点开头，而不是斜杠，例如`gopkg.in/check.v1`，`gopkg.in/yaml.v3`