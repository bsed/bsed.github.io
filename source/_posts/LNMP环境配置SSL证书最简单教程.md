---
title: "LNMP环境配置SSL证书最简单教程"
categories: [ "Linux" ]
tags: [ "linux","nginx","ssl" ]
draft: false
slug: "lnmp-environment-configuration-ssl-certificate-is-the-most-simple-tutorial"
date: "2016-08-23 21:33:00"
---

现在腾讯提供免费的一年证书，地址：[https://console.qcloud.com/ssl](https://console.qcloud.com/ssl)
![qcloud_ssl.png][1]
nginx重定向 http 自动跳转 https
```
server
    {
        listen 80;
        server_name app.typecho.me;
        index index.html index.htm index.php;
        rewrite ^/(.*)$ https://tygasoft.com/$1 permanent;
    }
```
配置ssl证书：


<!--more-->


```
server
    {
        listen 443;
        #listen [::]:80;
        server_name kelvin.mbioq.com;
        index index.html index.htm index.php;
        root  /app;

        ssl on;
        ssl_certificate /ssl/kelvin.mbioq.com_cert.crt;
        ssl_certificate_key /ssl/kelvin.mbioq.com.key;

        include typecho.conf;
        #error_page   404   /404.html;
        include enable-php-pathinfo.conf;

        location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$
        {
            expires      30d;
        }

        location ~ .*\.(js|css)?$
        {
            expires      12h;
        }

        location ~ /\.
        {
            deny all;
        }

        access_log off;
    }
```
安装完成。腾讯免费SSL证书+LNMP环境演示：[https://tygasoft.com/](https://tygasoft.com/)


  [1]: https://imgs.gnux.cn/usr/uploads/2016/11/2755623031.png