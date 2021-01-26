---
title: "Apache设置不允许ip直接访问网站"
categories: [ "Linux" ]
tags: [ "Apache" ]
draft: false
slug: "apache-settings-do-not-allow-direct-access-to-the-web-site-ip"
date: "2013-10-22 16:13:00"
---

解决了这个问题，在conf目录下的httpd.conf或者在conf\extra\httpd-vhosts.conf的最下面增加下面内容：
 

    <VirtualHost *:80>
        ServerName 你服务器的IP
        <Location />
        Order Allow,Deny
        Deny from all
        </Location>
    </VirtualHost>

