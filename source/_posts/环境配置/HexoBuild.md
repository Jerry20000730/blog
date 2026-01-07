---
title: Hexo搭建网站
tags: [Hexo]
categories: 环境配置
cover: images/HexoBuild/cover.png
password: wocaonima
abstract: The reason why this passage has been encrypted might be due to the fact that the blog contains (1) personal information, (2) private configuration (e.g., mac address, ip addresses), or (3) other reasons.
message: 请输入密码以访问内容 Please input the password to view the content
excerpt: 不小心手贱，把之前的网站删了，于是决定重新来一次，反正上一次建站也是非常的混乱
---

# 现在已经全面迁移至Github Pages，这篇仅限于你想自己用服务器的

### 前情提要
不小心手贱，把之前的网站删了，于是决定重新来一次，反正上一次建站也是非常的混乱

### 参考资料 (Reference)

1. Hexo官网：[https://hexo.io/zh-cn/](https://hexo.io/zh-cn/)
2. Wikitten Github: [https://github.com/zthxxx/hexo-theme-Wikitten#hexo-theme-wikitten](https://github.com/zthxxx/hexo-theme-Wikitten#hexo-theme-wikitten)

### Prerequisite
1. Node.js (Node.js 版本需不低于 10.13，建议使用 Node.js 12.0 及以上版本)
2. git


### 1. 安装Hexo
```bash
$ npm install -g hexo-cli
```
### 2. 初始化本地文件
在指定文件路径下（本人folder路径：/user/guofangcheng/Documents/Blog）新建一个文件夹Blog
下面的操作请在要生成的文件夹上一级使用（比如上面的话就是在/user/guofangcheng/Documents路径下使用下面命令，确保不会文件夹套娃）
```bash
$ hexo init <folder>
$ cd <folder>
$ npm install
```
新建完成后，指定文件夹的目录如下：
```
.
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
```
验证是否完整下载完所有所需文件，可以在终端里运行：
```bash
$ hexo server
```
你会看到如下的界面，代表初始化成功：
![first index](/images/HexoBuild/first_index.png)

### 3. 更换主题
(官网原话) 创建 Hexo 主题非常容易，您只要在 `themes` 文件夹内，新增一个任意名称的文件夹，并修改 `_config.yml` 内的 `theme` 设定，即可切换主题。一个主题可能会有以下的结构：
```
.
├── _config.yml
├── languages
├── layout
├── scripts
└── source
```
这里需要注意的是，建站根目录的`_config.yml`与`theme`文件夹下的`_config.yml`不同，前者为网站整体的配置信息，后者为主题的配置文件。（为区分两种文件，网站整体的文件称为**站点配置文件**，主题文件夹下的称为**主题配置文件**）
这一次我使用的是hexo-theme-Wikitten (网址：[https://github.com/zthxxx/hexo-theme-Wikitten#hexo-theme-wikitten](https://github.com/zthxxx/hexo-theme-Wikitten#hexo-theme-wikitten)) 并依照README.md文件里的操作进行

#### 安装Wikitten theme

1. 进入Hexo站点文件夹，克隆 Wikitten 主题到 themes/ 路径下
```bash
$ git clone https://github.com/zthxxx/hexo-theme-Wikitten.git themes/Wikitten
```

2. 覆盖站点目录中一些默认页面模板
```bash
$ cp -rf themes/Wikitten/_source/* source/
$ cp -rf themes/Wikitten/_scaffolds/* scaffolds/
```

3. 重命名主题中的 `_config.yml.example` 到 `_config.yml`，然后可以使用配置文件配置主题
```bash
$ cp -f themes/Wikitten/_config.yml.example themes/Wikitten/_config.yml
```
根据原作者推荐，站点配置文件修改为：
```
# Hexo Configuration
# URL
permalink: wiki/:title/

# Directory
skip_render:
- README.md
- '_posts/**/embed_page/**'

# Writing
new_post_name: :title.md # File name of new posts

## Markdown
## https://github.com/hexojs/hexo-renderer-marked
marked:
gfm: true

## Plugins: https://hexo.io/plugins/
### JsonContent
jsonContent:
meta: false
pages:
title: true
date: true
path: true
text: true
posts:
title: true
date: true
path: true
text: true
tags: true
categories: true
ignore:
- 404.html

### Creat sitemap
sitemap:
path: sitemap.xml

### Adds nofollow attribute to all external links in your hexo blog posts automatically.
nofollow:
enable: true
exclude:
- <your site url domain> # eg: zthxxx.me
```
修改主题配置文件中的部分功能，如`profile` `social_links`和`history_control`:
```
# Customize
customize: # 首先修改这项里面的信息为你自己的各项信息
    sidebar: left # 侧边栏的所在位置，默认左边
    category_perExpand: false # 侧边栏里的各个分类是否默认全部展开
    default_index_file: index.md # 是否指定一篇文章作为首页来代替默认的多篇文章的首页，没有此项的话就会显示默认的按时间顺序排列的文章
    
# Widgets
widgets: # 挂件，默认指开启了分类这一栏
    - category
    # - recent_posts
    # - archive
    # - tag
    # - tagcloud
    # - links
    
# History version 
history_control: # 启用这一项使得 wiki 能有历史版本的功能（查看源码、在线编辑、对比历史变动）
    enable: true
    server_link: https://github.com # 版本控制服务器，推荐使用 GitHub https://github.com
    user: Jerry20000730
    repertory: MyHexoBlog
    branch: writing
```

4. 在**站点目录**下，你可以通过以下命令安装他们：
```bash
npm i -S hexo-autonofollow hexo-directory-category hexo-generator-feed hexo-generator-json-content hexo-generator-sitemap
```

5. 配置mathjax渲染（可选）：

首先安装pandoc（在站点目录下），同时在hexo站点下修改渲染引擎：
```bash
$ npm un hexo-renderer-marked --save
$ npm i hexo-renderer-pandoc --save # or hexo-renderer-krammed
```
然后将以下配置加到站点`_config.yml`文件中：
```
math:
  enable: true
  engine: mathjax
```
<!--more-->