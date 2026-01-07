---
title: Docker Hub 镜像命名规则
tags: [Docker]
categories: Cloud-Native
cover: images/CloudNative/docker/cover.png
excerpt: 官方认证的镜像或者是自己上传的镜像，这些命名规则有什么规律？
---

# Docker Hub上的镜像命名规则是什么

镜像会分版本，虽然直接使用latest固然方便，但是在生产环境不可以这么做，会导致版本不可控（版本太重要了）。

通常来说，镜像标签的格式是**应用的版本号+操作系统**，其中版本号为**主版本号+次版本号+补丁号**，有的还会再正式发布前出rc版本（release candidate）。

有的标签还会加上`slim`，`fat`来进一步表示这个镜像的内容是经过精简的，还是包含了比较多的辅助工具，通常`slim`镜像会比较小，运行效率高，而`fat`镜像会比较大，适合用来进行开发调试。

> 胖容器 和 瘦容器
> 胖容器 = “一锅端”打进去的操作系统 + 运行时 + 应用，可立即跑，体积大、攻击面广、更新重。
> 瘦容器 = 仅保留跑起来必需的内容，多阶段 / distroless 制作，体积小、安全、传输快，但打包和排错更精细。

# 如何上传自己的镜像

1. Docker Hub上注册一个用户
2. 在本机上使用`docker login`功能，用注册的用户名和密码认证身份登录
3. 【重要】`docker tag`命令，给镜像改成带用户名的完整名字，表示镜像是属于这个用户的，或者你直接在`docker build -t`在创建镜像的时候就起好名字

```
docker tag ngx-app tragicmaster/ngx-app:1.0
docker images
```
4. docker push

# 离线环境该怎么办

使用Docker Hub来管理镜像是非常方便，但是企业内网的离线环境，自然也就不能用`docker push`，`docker pull`来推送

最佳的方法是在内网环境里伪造Docker Hub，创建一个自己私有的Registry服务，就像自己搭一个Github一样，目前自建Registry有很多成熟的解决方案，比如Docker Registry，CNCF Harbor，腾讯内部使用的是Docker Registry。

