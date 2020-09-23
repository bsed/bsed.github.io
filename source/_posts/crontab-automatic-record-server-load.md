---
title: Crontab自动记录服务器负载
date: 2016-11-09 20:53:00
updated: 2016-11-21 20:54:46
tags: 
- linux
- nginx
- ssl
categories: 
- linux

---
每分钟自动记载uptime，并记录到log文件。
```
crontab -e
```
然后，在crontab中添加以下自动命令：
```
* * * * * uptime>>/home/wwwlogs/load.log
```
以上规则为每分钟自动记录。如果不行，可以手动执行一下。再给755权限
```
chmod 755 /home/wwwlogs/load.log
```
