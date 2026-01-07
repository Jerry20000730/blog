---
title: "Git Cheatsheet Part 1: Overview"
tags: [git]
categories: Git
cover: images/Git/cover.png
excerpt: This section mainly talks about fundamental concepts in git.
---

## Table of Contents

- [初始化](#%E5%88%9D%E5%A7%8B%E5%8C%96)
- [创建仓库](#%E5%88%9B%E5%BB%BA%E4%BB%93%E5%BA%93)
- [Git的四个区域](#git%E7%9A%84%E5%9B%9B%E4%B8%AA%E5%8C%BA%E5%9F%9F)
- [Git的三种状态](#git%E7%9A%84%E4%B8%89%E7%A7%8D%E7%8A%B6%E6%80%81)
- [特殊文件(夹)](#%E7%89%B9%E6%AE%8A%E6%96%87%E4%BB%B6%E5%A4%B9)
- [基本分支](#%E5%9F%BA%E6%9C%AC%E5%88%86%E6%94%AF)

# 初始化
初始化设置用户名和邮箱
```shell
git config --global user.name "Your Name"
git config --global user.email "email@email.com"
git config --global credential.helper store
```

# 创建仓库
创建一个新的本地仓库（忽略project-name则在当前目录创建）
```shell
git init <project-name>
```
下载一个远程仓库
```shell
git clone <url>
```

# Git的四个区域
- `工作区 (working directory)`: 就是你在电脑里看到的目录
- `暂存区 (Stage/Index)`: 一般存放在`.git`目录下面
- `本地仓库 (Repository)`: 工作区有一个隐藏目录的`.git`，这个不算工作区，而是Git的版本库
- `远程仓库 (Remote)`: 托管在远程服务器上的仓库

# Git的三种状态
1. `已修改 (Modified)`: 修改了文件，但没保存到暂存区
2. `已暂存 (Staged)`: 把修改后的文件放到了暂存区
3. `已提交 (Commited)`: 把暂存区的文件提交到本地仓库

# 特殊文件(夹)
- `.git`: Git仓库的元数据和对象数据库
- `.gitignore`: 配置不需要提交到仓库的文件
- `.gitattribute`: 指定文件的属性，比如换行符
- `.gitkeep`: 使得空目录也能提交到仓库
- `.gitmodules`: 记录子模块的信息
- `.gitconfig`: 记录仓库的配置信息

# 基本分支
- `main`: 默认主分支
- `origin`: 默认远程仓库
- `HEAD`: 指向当前分支的指针
- `HEAD^`: 上一个版本
- `HEAD~4`: 上四个版本
