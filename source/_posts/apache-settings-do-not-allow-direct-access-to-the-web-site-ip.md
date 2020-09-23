---
title: Apache设置不允许ip直接访问网站
date: 2013-10-22 16:13:00
updated: 2015-01-11 16:14:41
tags: 
- MVI
categories: 
- java
- php

---
解决了这个问题，在conf目录下的httpd.conf或者在conf\extra\httpd-vhosts.conf的最下面增加下面内容：
 

    <VirtualHost *:80>
        ServerName 你服务器的IP
        <Location />
        Order Allow,Deny
        Deny from all
        </Location>
    </VirtualHost>

