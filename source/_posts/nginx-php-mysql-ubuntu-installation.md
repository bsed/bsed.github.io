---
title: ubuntu nginx php mysql 安装
date: 2015-07-05 09:30:00
updated: 2015-08-29 18:13:41
tags: 
- ubuntu
- apache2
categories: 
- linux

---
# 1、先更新ubuntu系统

更新命令

    sudo apt-get update
    sudo apt-get upgrade

添加ubuntu nginx更新源镜像

    cd /etc/apt/
    sudo cp sources.list sources.list_bak

  


<!--more-->


将如下代码添加到sources.list文件中

    deb http://nginx.org/packages/ubuntu/ precise nginx 
    deb-src http://nginx.org/packages/ubuntu/ precise nginx

使用

sudo vi sources.list

如果提示：W: GPG 错误：http://nginx.org precise Release: 由于没有公钥，无法验证下列签名： NO_PUBKEY ABF5BD827BD9BF62
由于官方不信任该源

解决方法： `sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys ABF5BD827BD9BF62`



# 3、更新和安装update and install

    sudo apt-get update 
    sudo apt-get install nginx



# 4、启动nginx

`sudo /etc/init.d/nginx start`

# 5、check version

`nginx -v`

# 6、配置php+mysql

`sudo apt-get install php5-cli php5-cgi mysql-server php5-mysql`


# 7、安装FastCgi

`sudo apt-get install spawn-fcgi`


# 8、配置nginx

## 8.1、修改nginx的配置文件：/etc/nginx/conf.d/default 修改主机名

`server_name localhost;`

## 8.2、修改index的一行，添加index.php

`index index.php index.html index.htm;`

## 8.3、去掉下面部分的注释用于支持 php 脚本：

    location ~ .php$ {     
      include /etc/nginx/fastcgi_params; #需放在第一行，否则会出错    
      fastcgi_pass 127.0.0.1:9000;    
      fastcgi_index index.php;    
      fastcgi_param SCRIPT_FILENAME /var/www/nginx-default$fastcgi_script_name; 
    }



# 9、 重新启动nginx

    /etc/init.d/nginx stop 
    /etc/init.d/nginx start



# 10、启动fastcgi php

`spawn-fcgi -a 127.0.0.1 -p 9000 -C 10 -u www-data -f /usr/bin/php-cgi`
杀死spawn-fcgi进程　`killall -9 -w php-cgi`
www-data 是nginx的　用户名　
user  www-data;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;

# 11、nginx提示502 错误

nginx 502 Bad Gateway没有启动，启动命令是：

`spawn-fcgi -a 127.0.0.1 -p 9000 -C 10 -u www-data -f /usr/bin/php-cgi`


# 12、设置开机自启动

Ubuntu开机之后会执行/etc/rc.local文件中的脚本 

所以我们可以直接在/etc/rc.local中添加启动脚本。

spawn-fcgi -a 127.0.0.1 -p 9000 -C 10 -u www-data -f /usr/bin/php-cgi 添加到语句： exit 0

前面才行 如下图：
![2015-08-29 10:28:51.png][1]

#13、no input file specified错误

`sudo vi /etc/nginx/sites-available/default`

其中这个字段

    location ~ .php$ { 
    
    root html; 
    
    fastcgi_pass 127.0.0.1:9000;
    
     fastcgi_index index.php; 
    
    fastcgi_param SCRIPT_FILENAME /var/www/nginx-default$fastcgi_script_name;
    
     include fastcgi_params;
    
     }

注意

fastcgi_param SCRIPT_FILENAME /var/www/nginx-default$fastcgi_script_name;

/var/www/nginx-default 改为你的网站根目录，一般就是改成这个。 

server 字段下root 目录和网站根目录保持一致


  [1]: https://imgs.gnux.cn/usr/uploads/2015/08/4221645265.png