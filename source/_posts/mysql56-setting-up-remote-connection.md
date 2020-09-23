---
title: Mysql5.6设置远程连接
date: 2012-01-19 13:12:00
updated: 2020-01-21 14:06:16
tags: 
- vue
- colorui
categories: 
- design

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

