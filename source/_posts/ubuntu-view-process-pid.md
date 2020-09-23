---
title: ubuntu 查看进程pid
date: 2012-11-15 09:15:00
updated: 2016-11-20 09:16:07
tags: 
- linux
- ssh
- scp
categories: 
- linux

---
查看pid

ps laf

查看使用内存情况

ps aux|grep process_name
查看/proc/process_id/文件夹下的status文件
top -d 1 -p pid [,pid …] //设置为delay 1s，默认是delay 3s