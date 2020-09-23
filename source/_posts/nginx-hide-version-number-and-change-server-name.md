---
title: nginx隐藏版本号及更换服务器名称
date: 2015-09-05 11:56:00
updated: 2015-09-06 11:57:52
tags: 
- java
- xml
categories: 
- java

---
## 服务器环境

nginx的目录为：/webdata/server/nginx

备份目录：/webdata/backup/

### 隐藏版本号
### 备份配置文件

`tar -cvf /webdata/backup/nginx.conf.20140623.tar /webdata/server/nginx/conf/`

### 修改配置
`vi /webdata/server/nginx/conf/nginx.conf`


<!--more-->


### http中加入：
`server_tokens off;`
`vi /webdata/server/nginx/conf/fastcgi.conf`
修改
`fastcgi_param  SERVER_SOFTWARE    nginx/$nginx_version;`
为
fastcgi_param  SERVER_SOFTWARE    nginx;
## 重新加载配置文件
`/webdata/server/nginx/sbin/nginx -s reload`

PS: 重启前最好用-t参数检测配置文件是否正确
### 验证

curl --head 127.0.0.1