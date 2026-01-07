---
title: 居家无线网配置方案
tags: [Network]
categories: 家中网络环境配置
cover: images/Network/cover.jpeg
excerpt: 家中上网一直都是对于我而言是一个很大的问题
---

# 背景

**网很重要，没网会死**

# 方案设计原则

1. **可插拔**：要了可以立刻连接，不要了能立刻恢复
2. **范围可控**：想连的可以连，不想连的不让连
3. **客制化**：可以根据个人需求进行客制化的定制
4. **无感连接**：你没有“开”和“关”的这个动作

# 当前租房内设计方案

![home network structure](/images/Network/home_network_structure.png)

这里画了一个当前的网络方案的简化图（其实也没啥好画的）本质就是需要连接旁路由的设备会直接将网关和dns设置为预留给Apple TV的`192.168.31.222`，由Apple TV进行代理转发。

# 软件选型
![stash](/images/Network/stash.png)
Stash: Stash 是 Clash Premium 内核在 Apple 设备上的最佳实现客户端，使用该客户端的原因有以下几个原因

1. **Apple友好**：家中大多数设备为苹果设备，基于 Apple 平台特性，Stash 在 Clash Premium 功能基础上，支持配置同步：通过 iCloud 同步到所有设备上，实现一次配置，多设备共享。
2. **快捷操作**：用户可以通过小组件、Today 视图、Widget、Siri 快捷指令等方式快速启动、停止、切换节点等操作 Stash。
3. **支持为局域网设备提供代理**：这个也是选Apple TV的重要原因，Stash tvOS 支持在 Apple TV 上启用透明代理

> 注意：支持的Apple TV型号必须是Apple TV 4K 2代及以上

# 设置无感代理方式
## iPhone/iPad
进入 设置 - 无线局域网 - 选中连接的WiFi - 将IPv4由“自动”设置为“手动”，如下图

![iPhone Settings](/images/Network/iPhone.png)

将IP地址设置为当前连接的局域网IP地址（可以在路由器界面进行设置静态ip），路由器（即网关）设置为Apple TV的局域网ip地址

## Mac
进入 系统设置 - Wi-Fi - 选中连接的WiFi - 详细信息

![Mac Settings](/images/Network/Mac.png)

选中左边的**TCP/IP**，与iPhone类似，将IP地址设置为当前连接的Apple TV的局域网IP地址，路由器（即网关）设置为Apple TV的局域网ip地址

![Mac Settings Step 2](/images/Network/Mac2.png)

选中左边的**DNS**，将DNS服务器添加当前连接的Apple TV的局域网IP地址

![Mac Settings Step 3](/images/Network/Mac3.png)

> 坑点：这个是全局的哦~设置完毕之后出去之后要恢复回来不然就连不上外面的网了

## Windows
点击右下角网络状态栏 - 选择WLAN  - 选择网络的详情

![Windows Settings Step 1](/images/Network/windows.png)

进入后设置ip地址，网关地址和dns服务器

![Windows Settings Step 2](/images/Network/windows2.png)

# 增强功能【此步骤需要iPhone与Apple TV配合使用】

## 覆写功能 Override
官方文档地址：[https://stash.wiki/configuration/override](https://stash.wiki/configuration/override)

大家熟悉用机场的都知道，机场给予的配置文件是固定的yaml文件，且每一次更新都会进行更新（clash手动添加规则后,每次订阅更新都会将自定义的规则清理），以往clash等一些客户端会提供一些自定义规则的功能（如Clash for Windows支持prepend-rules）

![Clash for Windows支持自定义规则与远端规则合并](https://linux.do/uploads/default/original/2X/2/237100990184be1a3245ae6b2e9498ac3fffdf5e.png)

Stash 也支持这一类似功能，但是提供了我认为更强大更通用的，就是这个Override功能，Override允许用户修改配置文件的部分字段（不止配置规则），按功能点划分覆写文件

> 覆写文件使用 YAML 格式，文件后缀为 .stoverride

假设我需要添加一个自定义规则，如`DOMAIN-SUFFIX tv.micu.hk DIRECT`，那么我就可以在github上托管一个配置文件，如custom_rule.stoverride

```yaml
name: 自定义规则
desc: 相较于一些机场所提供的专用的规则配置，如果有需要进行额外配置的，可以在这边加，使用版本方便维护
#!desc=自定义规则
rules:
  - DOMAIN-SUFFIX,micu.hk,DIRECT
```

并将这个覆写脚本在iPhone的Stash客户端中进行配置

![override step 1](/images/Network/override1.png)

然后添加这个规则（目前只支持url更新，所以最好是在github上创建好后点击raw获取链接）

![override step 2](/images/Network/override2.png)

最后一步是部署（部署到我的Apple TV）,最下方选择设置 - 配置同步

![override step 3](/images/Network/override3.png)

选择部署到我的Apple TV上

> 注意：Apple TV需要登录与iPhone同一个iCloud账号，否则无法同步，且Apple TV的stash app上要选择配置文件为iCloud中的
> ![](/images/Network/tvos-stash.png)

![override step 4](/images/Network/override4.png)

> 注意：这里有一些经验之谈就是，部署完毕后要登录到Apple TV的stash上去，不然有可能更新的覆写功能没有激活。