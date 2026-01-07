---
title: 部署SSL证书
tags: [SSL]
categories: 环境配置
cover: images/SSL/cover.jpeg
excerpt: 记录一下SSL证书配置（SSL Certificate Deployment）
---

<!-- toc -->

# Prerequisite
1. 一个域名
2. Nginx服务器

# 申请免费证书 (腾讯云)

1. 登录 [SSL 证书控制台](https://console.cloud.tencent.com/ssl)，进入**我的证书**页面，并单击**申请免费证书**。
2. 填写证书申请表单，如下图所示：
![apply for free certificate at Tencent Cloud](/images/SSL/free_certificate.png)
其中：
- 证书绑定域名：网站域名
- 域名验证方式：一般选手动就可以，然后到域名解析商配置一下，因为本人是腾讯云买的，就比较方便，只需要到[https://console.cloud.tencent.com/cns/domains](https://console.cloud.tencent.com/cns/domains)
   - 详情参考：[https://cloud.tencent.com/document/product/400/54500](https://cloud.tencent.com/document/product/400/54500)
- 申请邮箱：请输入您的邮箱地址。
- 算法选择：RSA就行

# 安装SSL证书（Nginx）
以下针对于使用Nginx进行反向代理部署的服务器

## 下载SSL文件
等待审核通过之后，点击下载按钮并获取SSL证书
![click download](/images/SSL/download_button.png)
依据不同云服务器商要求下载对应版本（我下的是Nginx版本）
![choose SSL download version](/images/SSL/download_version.png)

**文件夹中会含有以下几个文件：**

**证书文件**：xxx.crt 证书文件

**密钥文件**：xxx.key 私钥文件

**CSR文件**：xxx.csr 文件

**PEM文件**: xxx.pem 文件

我们只需要`.crt`和`.key`文件即可

## 登录远程服务器
WinSCP/PuTTY/我喜欢用Termius

1. 将已获取到的 cloud.tencent.com_bundle.crt 证书文件和 cloud.tencent.com.key 私钥文件从本地目录拷贝到轻量应用服务器 Nginx 默认配置文件目录中
2. 输入grep命令获取nginx配置目录（知道的略过这步）
```shell
find / | grep nginx.conf
```

3. 然后去配置文件`nginx.conf`或者`site-enabled/default`(我是后者)配置文件增加下列行数
```shell
server {
    listen 443 ssl default_server;
    listen [::]:443 ssl default_server;
    ssl_certificate cert/tragicmaster.website_bundle.crt;
    ssl_certificate_key cert/tragicmaster.website.key;
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;
}
```

4. 保存并使用以下命令检测nginx配置是否正确
```shell
nginx -t
```
如果返回
```shell
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```
然后重新加载nginx (非常重要！不然换证书的时候容易检测不到)
```shell
nginx -s reload
```

5. 登录域名https://tragicmaster.website左上角成功变锁！

# Reference
1. Nginx服务器证书安装 [https://cloud.tencent.com/document/product/1207/47027](https://cloud.tencent.com/document/product/1207/47027)
2. linux下查看nginx的安装路径 [https://blog.csdn.net/sinat_22387459/article/details/115007510](https://blog.csdn.net/sinat_22387459/article/details/115007510)
3. Nginx 安装 SSL 配置 HTTPS超详细完整全过程 [https://developer.aliyun.com/article/761236](https://developer.aliyun.com/article/761236)
