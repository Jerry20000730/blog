---
title: Screen 命令
tags: [Screen]
categories: Linux
cover: images/Linux/Screen/cover.png
excerpt: In some cases, you are connected to a remote machine via SSH and perform a log-running task, and suddenly your SSH connection drops. If you have experienced this before (sadly I have), then screen is your best tool. Screen is a terminal multiplexer that allows us to resume the dis-connected sessions. You can create multiple terminal sessions and share your screen with other users using the screen.
---

## Table of Contents

- [Overview](#overview)
- [安装Screen](#%E5%AE%89%E8%A3%85screen)
- [创建Screen](#%E5%88%9B%E5%BB%BAscreen)
- [强制创建Screen](#%E5%BC%BA%E5%88%B6%E5%88%9B%E5%BB%BAscreen)
- [将指定的Screen作业在后台工作](#%E5%B0%86%E6%8C%87%E5%AE%9A%E7%9A%84screen%E4%BD%9C%E4%B8%9A%E5%9C%A8%E5%90%8E%E5%8F%B0%E5%B7%A5%E4%BD%9C)
- [显示当前所有Screen作业](#%E6%98%BE%E7%A4%BA%E5%BD%93%E5%89%8D%E6%89%80%E6%9C%89screen%E4%BD%9C%E4%B8%9A)
- [恢复、链接Screen对话](#%E6%81%A2%E5%A4%8D%E9%93%BE%E6%8E%A5screen%E5%AF%B9%E8%AF%9D)
- [尝试恢复Screen对话](#%E5%B0%9D%E8%AF%95%E6%81%A2%E5%A4%8Dscreen%E5%AF%B9%E8%AF%9D)
- [也用于恢复名字为name的会话](#%E4%B9%9F%E7%94%A8%E4%BA%8E%E6%81%A2%E5%A4%8D%E5%90%8D%E5%AD%97%E4%B8%BAname%E7%9A%84%E4%BC%9A%E8%AF%9D)
- [删除Screen对话](#%E5%88%A0%E9%99%A4screen%E5%AF%B9%E8%AF%9D)
- [Garbage Collection](#garbage-collection)

# Overview
Screen 的状态有两种，Attached 和 Detached，分别表示前台运行和后台运行。
使用 `screen -r 会话名 (或者编号)`可以将 Detached 状态的屏幕会话状态修改为 前台运行 ，并同时切换到这个会话中。如果会话状态已经是Attached状态，使用命令 `screen -r 会话名 (或者编号)`将会报错

# 安装Screen
```shell
$ sudo apt-get install screen
$ sudo yum install screen

// 验证
$ screen -v
```
# 创建Screen
```shell
$ screen -S <会话名>
```

# 强制创建Screen
```shell
// 即使目前已在作业中的screen作业，仍强制建立新的screen作业。
$ screen -m <会话名>
```

# 将指定的Screen作业在后台工作
```shell
// 将这个会话调为后台工作
$ screen -d <会话名>
// 将当前的会话调为后台工作，并恢复这个会话
$ screen -d -r <会话名>
```
# 显示当前所有Screen作业
```shell
$ screen -ls
```

# 恢复、链接Screen对话
```shell
// 连接 screen_id 为 2276 的 screen终端
$ screen -r 2276 
```

# 尝试恢复Screen对话
```shell
// 先试图恢复离线的作业。若找不到离线的作业，即建立新的screen作业。
$ screen -R 2276
```

# 也用于恢复名字为name的会话
```shell
// 当上面方法不行的时候
$ screen -x <会话名>
```

# 删除Screen对话
```shell
$ screen -S <会话名> -X quit
```

# Garbage Collection
```shell
$ screen -wipe
// 检查目前所有的screen作业，并删除已经无法使用的screen作业。
```

