---
title: Multi Domain With Different Reverse Proxy
tags: [Nginx]
categories: 环境配置
cover: images/Domain/cover.jpg
excerpt: How to work with one server if you only have one server, one ip, but multiple website you want to deploy when you are poor student :(
---
<!-- toc -->

# Multi Domain With Different Reverse Proxy
## Scenario
If you have one domain name, that is fine. However, if you have multiple domain with multiple website, but you only have one physical machine (also means you only have one public ip address). The way to deal with this is to do a little bit setup in nginx.conf

## My original setup
I have a machine that hosts my blog (which is this one) at https://www.tragicmaster.website. So my original setting (also can see in my SSL build blog [here](https://tragicmaster.website/wiki/环境配置/SSLBuild/#more). 
```
# Default server configuration
server {
        listen 80 default_server;
        listen [::]:80 default_server;

        # SSL configuration
        listen 443 ssl default_server;
        listen [::]:443 ssl default_server;
        ssl_certificate cert/tragicmaster.website.crt;
        ssl_certificate_key cert/tragicmaster.website.key;
        ssl_session_timeout 5m;
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_prefer_server_ciphers on;

        server_name tragicmaster.website;

        location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                try_files $uri $uri/ =404;
        }
}
```

And now I have a container running on localhost:8000, which is my second website that I want other people to access.

I tried several ways while this works for me: I create another setting so that when nginx see the domain name that I use is 'https://www.duber.site', it will redirect to the localhost:8000.

```
server {
    listen 80;
    listen [::]:80;
    server_name www.duber.site;


    location / {
        proxy_pass http://localhost:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # Add any additional SSL configurations if needed
    listen 443 ssl;
    listen [::]:443 ssl;
    ssl_certificate cert/duber.site.crt;
    ssl_certificate_key cert/duber.site.key;
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;

    # Add any other server configurations as needed
}
```

This is way how one ip, multi domain and multi application running together.