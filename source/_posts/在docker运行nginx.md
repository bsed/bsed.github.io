---
title: "在docker运行nginx"
categories: [ "Linux" ]
tags: [ "debian","nginx","docker" ]
draft: false
slug: "running-nginx-at-docker"
date: "2018-02-02 15:47:00"
---

## 准备

```bash
root@debian:~/nginx# docker pull nginx
Using default tag: latest
latest: Pulling from library/nginx
Digest: sha256:285b49d42c703fdf257d1e2422765c4ba9d3e37768d6ea83d7fe2043dad6e63d
Status: Image is up to date for nginx:latest
```
<!--more-->

conf存放nginx的配置文件，log存放nginx的日志，www存放模板文件，见下图

```bash
root@debian:~/nginx# tree
.
├── conf
│   └── nginx.conf
├── log
│   ├── access.log
│   └── error.log
└── www
    ├── conf
    │   └── nginx.conf
    ├── index.html
    ├── log
    └── www

7 directories, 4 files

```
以下是nginx.conf内容

```nginx
user root;
worker_processes auto;
pid /run/nginx.pid;

events {
    worker_connections 768;
    # multi_accept on;
}

http {

    ##
    # Basic Settings
    ##

    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;
    # server_tokens off;

    # server_names_hash_bucket_size 64;
    # server_name_in_redirect off;

    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    ##
    # SSL Settings
    ##

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2; # Dropping SSLv3, ref: POODLE
    ssl_prefer_server_ciphers on;

    ##
    # Logging Settings
    ##

    access_log /root/nginx/log/access.log;
    error_log /root/nginx/log/error.log;

    ##
    # Gzip Settings
    ##

    gzip on;
    gzip_disable "msie6";

    ##
    # Virtual Host Configs
    ##

    server {
        listen 80 default_server;
        listen [::]:80 default_server;

        root /root/nginx/www;

        # Add index.php to the list if you are using PHP
        index index.html index.htm index.nginx-debian.html;

        server_name _;

        location / {
            # First attempt to serve request as file, then
            # as directory, then fall back to displaying a 404.
            try_files $uri $uri/ =404;
        }

    }
}
```

以上这个配置从nginx的默认配置文件复制出来的，改动**user** 、**log**、**root**相关内容，即：

```
...nginx
user root;
access_log /root/nginx/log/access.log;
error_log /root/nginx/log/error.log;
root /root/nginx/www;
```

index.html

```html
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
</head>
<body>
<h2>Hello docker nginx, https://tygasoft.com</h1>
</body>
</html>
```

## 启动容器

```bash
root@debian:~/nginx# docker run -p 8080:80 --name mynginx  -v $PWD/conf/nginx.conf:/etc/nginx/nginx.conf -v $PWD/www:/root/nginx/www -v $PWD/log:/root/nginx/log  -d nginx
e192df48b6ec612aa0f97c4347d66909d732adf1d27d6911f4eab2c16227bf64
```

命令说明：

```
  -p 8080:80：将容器的80端口映射到主机的8080端口
  -name mynginx：将容器命名为mynginx
  -v $PWD/conf/nginx.conf:/etc/nginx/nginx.conf：将主机中当前目录下的nginx.conf挂载到容器的/etc/nginx/nginx.conf
  -v $PWD/www:/opt/nginx/www：将主机中当前目录下的www挂载到容器的/root/nginx/www
  -v $PWD/log:/opt/nginx/log：将主机中当前目录下的log挂载到容器的/root/nginx/log
```

------

## 测试是否正常

在浏览器打开 
![debian_nginx.png][1]


  [1]: https://imgs.gnux.cn/usr/uploads/2018/02/930569621.png