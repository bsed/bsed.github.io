---
title: "Mysql5.6设置远程连接"
categories: [ "SQL" ]
tags: [ "mysql" ]
draft: false
slug: "mysql56-setting-up-remote-connection"
date: "2012-01-19 13:12:00"
---

root权限设置远程连接
<!--more-->

```bash
mysql>use mysql;  
mysql> GRANT ALL PRIVILEGES ON *.* TO root@"%" IDENTIFIED BY "password";   
mysql> flush privileges;  
mysql> exit  
mysql>select host,user from user; //查看修改是否成功。
```

