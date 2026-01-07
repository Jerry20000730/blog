---
title: "Git Cheatsheet Part 3: Remote Repository"
tags: [git]
categories: Git
cover: images/Git/cover.png
excerpt: This section mainly talks about remote repository, specifically about how to create, view, delete and designate remote repository
---

## Table of Contents

- [远程仓库](#%E8%BF%9C%E7%A8%8B%E4%BB%93%E5%BA%93)

# 远程仓库
添加远程仓库
```shell
git remote add <remote-name> <remote-url>
```
查看远程仓库
```shell
git remote -v
```
删除远程仓库
```shell
git remote rm <remote-name>
```
重命名远程仓库
```shell
git remote rename <old-name> <new-name>
```
从远程仓库拉取代码
```shell
git pull <remote-name> <branch-name>
```
fetch默认远程仓库(origin)当前分支的代码，然后合并到本地分支
```shell
git pull
```
将本地改动的代码rebase到远程代码最新的代码上（为了有一个干净的线性的提交历史）
```shell
git pull --rebase
```
推送代码到远程仓库（然后再发起pull request）
```shell
git push <remote-name> <branch-name>
```
获取所有远程分支
```shell
git fetch <remote-name>
```
查看远程分支
```shell
git branch -r
```
fetch某一个特定的远程分支
```shell
git fetch <remote-name> <branch-name>
```