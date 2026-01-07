---
title: "Git Cheatsheet Part 4: Change, Restore and Reset"
tags: [git]
categories: Git
cover: images/Git/cover.png
excerpt: This section mainly talks about more advanced use to modify the commit history in local environment.
---

## Table of Contents

- [撤销和恢复](#%E6%92%A4%E9%94%80%E5%92%8C%E6%81%A2%E5%A4%8D)
- [修改信息查看](#%E4%BF%AE%E6%94%B9%E4%BF%A1%E6%81%AF%E6%9F%A5%E7%9C%8B)

# 撤销和恢复
移动一个文件到新的为位置
```shell
git mv <file> <new-file>
```
从工作区和暂存区删除一个文件，然后暂存删除操作
```shell
git rm <file>
```
只从暂存区删除一个文件，工作区保留
```shell
git rm --cached <file>
```
恢复一个文件到之前的版本
```shell
git checkout <file> <commit-id>
```
创建一个新的提交，用来撤销指定的提交，后者的所有变化都将被前者抵消，并且应用于当前分支
```shell
git revert <commit-id>
```
重置当前分支的HEAD为之前的某个提交，并且删除所有之后的提交。`--hard`参数表示重置工作区和暂存区，`--soft`参数表示重置暂存区，`--mixed`参数表示重置工作区
```shell
git reset --mixed <commit-id>
```
撤销暂存区的文件，重新放回工作区（git add的反向操作）
```shell
git restore --staged <file>
```

# 修改信息查看
列出还未提交的新的或修改的文件
```shell
git status
```
查看提交历史，`--oneline`可以忽略
```shell
git log --oneline
```
查看未暂存的文件更新了哪些部分
```shell
git diff
```
查看两个提交之间的差异
```shell
git diff <commit-id> <commit-id>
```