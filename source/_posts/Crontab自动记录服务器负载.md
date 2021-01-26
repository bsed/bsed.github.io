---
title: "Crontab自动记录服务器负载"
categories: [ "Linux" ]
tags: [ "linux","crontab" ]
draft: false
slug: "crontab-automatic-record-server-load"
date: "2016-11-09 20:53:00"
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
