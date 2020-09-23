---
title: LNMP 1.x版本 pathinfo设置
date: 2016-11-18 21:43:00
updated: 2016-11-27 17:26:38
tags: 
- mysql
- ubuntu
- bash
- navicat
categories: 
- linux

---
更新测试时间2016.03.08，lnmp1.3-beta最新版+typecho开发最新版。
只要修改vhost下面的配置文件 `enable-php.conf` 为 `enable-php-pathinfo.conf`
再引用 typecho.conf 伪静态文件，完美解决。
不用下面的废劲方法了。

===========分隔线============

现在使用lnmp1.x版本，搭配typecho登陆后台，会出现404错误。小夜就不深究原因了，这里只说解决办法。


<!--more-->


## 1.`enable-php.conf` 文件，添加`pathinfo2.conf`引入
```
        location ~ [^/]\.php(/|$)
        {
            try_files $uri =404;
            fastcgi_pass  unix:/tmp/php-cgi.sock;
            fastcgi_index index.php;
            include fastcgi.conf;
            include pathinfo2.conf;
        }
```
2.上传`pathinfo2.conf`文件（和原来默认版本不一样）
```
set $real_script_name $fastcgi_script_name;
if ($fastcgi_script_name ~ "(.+?\.php)(/.*)") {
set $real_script_name $1;
set $path_info $2;
}
fastcgi_param SCRIPT_FILENAME $document_root$real_script_name;
fastcgi_param SCRIPT_NAME $real_script_name;
fastcgi_param PATH_INFO $path_info;
```
## 3.如果除首页外全部404，则为伪静态规则问题，请使用以下伪静态规则：
```
location /
{
index index.html index.php;
if (-f $request_filename/index.html){
rewrite (.*) $1/index.html break;
}
if (-f $request_filename/index.php){
rewrite (.*) $1/index.php;
}
if (!-f $request_filename){
rewrite (.*) /index.php;
}
}
```
以上问题，主要常见于`lnmp1.3-beta`版本，主要为typecho遇到。
来源：[https://wiki.vpsmm.com/lnmp-pathinfo/](https://wiki.vpsmm.com/lnmp-pathinfo/)