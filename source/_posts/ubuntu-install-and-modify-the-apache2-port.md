---
title: Ubuntu15.4 安装和修改Apache2端口
date: 2014-12-15 11:32:00
updated: 2015-07-11 11:33:25
tags: 
- js
- angularjs
- debug
categories: 
- js

---
因为本机有Nginx，所以没有安装Apache2的需要。
 
不过今天为了学习如何使用icinga，还是学一下apache2吧，以后熟悉了icinga,再用Nginx将Apache2替换。
安装很简单：

`apt-get install apache2`
 
然后自动启动失败，因为我的Nginx占着80端口和443端口呢。
通过whereis apache2查找，发现/etc/apache2目录
好，打开该目录下的ports.conf文件，


<!--more-->


把所有的80端口改成81，443改成444.
然后看文件注释上说要/etc/apache2/sites-enabled/000-default文件，打开文件后
把

<VirtualHost *:80>  
修改成81，然后看到根目录在/var/www下面
到/var/www/目录看到有index.html文件

    <html><body><h1>It works!</h1>  
    <p>This is the default web page for this server.</p>  
    <p>The web server software is running but no content has been added, yet.</p>  
    </body></html>  

现在重新启动apache2
`service apache2 restart`

    * Restarting web server apache2                                                                                                                                                                                     apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1 for ServerName  
    ... waiting apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1 for ServerName  
                                                                                   [ OK ]  

可以打开/etc/apache2/apache2.conf文件
添加上一句：

`ServerName localhost ` 
 
再次重启后错误消失。
打开网页，输入localhost:81地址，看到Index.html页面显示出来了