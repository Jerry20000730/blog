---
title: "Git Cheatsheet Part 2: Command and Branches"
tags: [git]
categories: Git
cover: images/Git/cover.png
excerpt: This section mainly talks about fundamental commands in Git, including how to commit, push and pull the content, and how to use, merge and rebase branches.
---

## Table of Contents

- [添加和提交](#%E6%B7%BB%E5%8A%A0%E5%92%8C%E6%8F%90%E4%BA%A4)
- [分支](#%E5%88%86%E6%94%AF)
- [合并分支](#%E5%90%88%E5%B9%B6%E5%88%86%E6%94%AF)
- [Rebase](#rebase)

# 添加和提交
添加一个文件到仓库
```shell
git add <file>
```
添加所有文件到仓库
```shell
git add .
```
提交所有暂存区的文件到仓库
```shell
git commit -m "message"
```
提交所有已经修改的文件到仓库
```shell
git commit -am "message"
```

# 分支
查看所有本地分支
```shell
# 当前的分支前会有一个`*`
git branch
```
查看远程分支
```shell
git branch -r
```
查看所有分支
```shell
git branch -a
```
创建一个新分支
```shell
git branch <branch-name>
```
切换到指定分支，并更新工作区
```shell
git checkout <branch-name>
```
创建一个新分支，并切换到该分支
```shell
git checkout -b <branch-name>
```
删除一个已经合并的分支
```shell
git branch -d <branch-name>
```
删除一个分支，不管是否合并
```shell
git branch -D <branch-name>
```
给当前的版本打上标签，通常用于版本发布
```shell
git tag <tag-name>
```

# 合并分支
合并分支a到分支b，有两种模式，第一种是禁用Fast forward模式，参数为`--no-ff`，合并后的历史有分支，能看出曾经做过合并
```shell
git merge --no-ff -m "message" <branch-name>
```
第二种是使用Fast forward模式，参数是`--ff`，合并后历史会变成一条直线。原来是直接将仓库里的HEAD指针指向要merge的分支上。
```shell
git merge --ff -m "message" <branch-name> 
```
第三种是使用squash进行压缩，参数是`--squash`，是用来把一些不必要commit进行压缩，比如说，你的feature在开发的时候写的commit很乱，那么我们合并的时候不希望把这些历史commit带过来，于是使用–squash进行合并。
```shell
git merge --squash <branch-name>
```
三种方式的区别可以查看下图作为参考
![merge.png](/images/Git/Git2/merge.png)

# Rebase
Rebase操作可以将本地未push的分支提交历史整理成直线，假设当前有两个分支，master和feature。

官方原文解释为：_当执行rebase操作时，git会从两个分支的共同祖先开始提取待变基分支上的修改，然后将待变基分支指向基分支的最新提交，最后将刚才提取的修改应用到基分支的最新提交的后面。_

具体操作如下图：
![rebase_1.png](/images/Git/Git2/rebase_1.png)
当执行rebase命令时：
```shell
git checkout feature
git rebase master
```
此举主要用于我们在分支的feature上开发了一段时间，准备从主干上拉一下最新变动，下图为变基之后的提交节点图
![rebase_2.png](/images/Git/Git2/rebase_2.png)
结合例子看一下官方原文：当在feature分支上执行git rebase master时，git会从master和featuer的共同祖先B开始提取feature分支上的修改，也就是C和D两个提交，然后将feature分支指向master分支的最新提交上，也就是M。最后把提取的C和D接到M后面

注意：**这里的将C和D接到M后面的接法，会依次拿M和C、D内容分别比较，处理冲突后生成新的C’和D’**

**_为什么叫rebase？_**
feature分支是基于master分支的B拉出来的分支，feature的基底是B。而master在B之后有新的提交，就相当于此时要用master上新的提交来作为feature分支的新基底。
