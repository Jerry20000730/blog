---
title: Docker 镜像和 DockerFile
tags: [Docker]
categories: Cloud-Native
cover: images/CloudNative/docker/cover.png
excerpt: 镜像的本质是一层又一层的镜像层组成的，每层都是制度不可修改的一组文件
---

## 镜像的内部机制

镜像本质是一个打包文件，里面包含了应用程序还有它运行所依赖的环境，例如**文件系统**，**环境变量**，**配置参数**等。环境变量和配置参数相对比较简单，_真正麻烦的是**文件系统**_。为了保证容器运行环境的一致性，镜像必须把应用程序锁在操作系统的根目录都包含进来，虽然这些文件里不包含kernel（容器共享宿主机kernel），但是如果每个镜像都重复做这样的打包操作，会有大量的冗余。

很自然的，我们就会想到将重复的部分抽取出来，存放一份根目录文件（比如Ubuntu），然后让所有镜像以某种方式共享这部分数据，这种方式就是分层（Layer）

容器的内部是由多个镜像层组成的，每层都是只读不可修改的一组文件，相同的层可以在镜像之间共享，然后多个层像搭积木一样堆叠起来，再使用一种叫做“联合文件系统（Union FS）“的方式将他们合并在一起，就形成了容器最终看到的文件系统。

> 联合文件系统（Union File System， UnionFs）可以将不同物理位置的目录合并、挂载到同一个目录中，即便目录的物理位置是分开的。UnionFs 把文件系统的每一次修改作为一个个层进行叠加，同时可以将不同目录挂载到同一个虚拟文件系统下。如果一次同时加载多个文件系统，UnionFs 会把各层文件叠加起来，最终文件系统会包含所有底层文件和目录，从外部视角看，就是一个完成的文件系统。

> 容器镜像设计中，为了解决各类依赖以及依赖共享，正是利用 UnionFs 实现了镜像分层，再结合 bootfs、rootfs，一层层继承、叠加。启动容器时把相关的层挂载到一个目录，作为容器的根文件系统，这就是容器镜像的原理。

使用命令`docker inspect`可以查看镜像的分层信息，比如

```sh
docker inspect nginx:alpine
```

可以看到他的分层信息在"RootFS"的部分

```json
"RootFS": {
    "Type": "layers",
    "Layers": [
        "sha256:a16e98724c05975ee8c40d8fe389c3481373d34ab20a1cf52ea2accc43f71f4c",
        "sha256:2daa38453f3839c68c18e7af10f04bf2259d432c05dce454841e24a129b53009",
        "sha256:d4261a1a67711866f8c31560e2326a631464f86c0a711c843cf6026dece885e8",
        "sha256:7d5d0920762752b29bf4329267456cad14f3ee902ef40179eb466c7a4eeaf4e7",
        "sha256:b4896f69ba7afecb40f6badf4287761288eab9952c7132ab900098fb2f28c16d",
        "sha256:61b204e6ce207d2ef4ed25e628fb08eaa1326173b78a218164d70d353cac9438",
        "sha256:1b69eff9b9cc79db4b61e434fe73334618dc3b7077fb9eb095db31b278a768ef",
        "sha256:60841a212b982e785aa9aa5aa7e7eea1128e7143829dd0b9ab3cd481f27d5e3d",
        "sha256:43628cfc79f5984e0a5287e2855b2131b3597c3dac57cc4525fefc2cf0052d7d"
    ]
},
```
通过这个信息可以看出，nginx:alpine镜像由8个layer，所以你在使用`docker pull`的时候那些奇怪的输出信息是啥了

## Dockerfile是什么

知道了容器内部的结构和基本原理，就可以学习如何自己动手制作镜像了，也就是自己打包应用。如果我么将容器看成”现房“，镜像就是”样板间“，那么造出这个样板间我们需要”施工图纸“，施工图纸就是**Dockerfile**

假设我们有一个Dockerfile:

```
FROM busybox
CMD echo "hello world"
```

那么这个文件里只有两个指令，第一条指令是FROM，所有的Dockerfile都要从他开始，表示构建使用的基础镜像。第二条指令是CMD，它指定`docker run`启动容器时默认执行的命令。有了”施工图纸“，就可以使用`docker build`命令创建出镜像：

```
docker build -f Dockerfile.busybox .

Sending build context to Docker daemon 7.68kB
Step 1/2 : FROM busybox
 ---> d38589532d97
Step 2/2 : CMD echo "hello world"
 ---> Running in c5a762edd1c8
Removing intermediate container c5a762edd1c8
 ---> b61882f42db7
Successfully built b61882f42db7
```

其中`-f`参数指定Dockerfile文件名，后面必须跟一个文件路径，称为**构建上下文（build context）**

从命令输出也能看出，`docker build` 不是在本地运行的，而是将所有的目录和运行环境打包传给远端，通过Docker daemon进行拉取镜像，打包，包装到容器中，参考下面这张图：

![docker principle](/images/CloudNative/docker/docker_principle.png)

注意：使用上下文查找文件只能查找一级，不能递归向下查找第二级，比如：

![example file structure](/images/CloudNative/docker/example.png)

如果指定路径为.，也就是当前路径，那么如果我想执行命令

```
COPY hello.txt .
```

就会报错，因为`hello.txt`在`docker`文件夹下面，不在helloworld-app的文件下，但是上下文只会查询一级，不会向下查询第二级。