---
title:  Liunx怎么查看zombie进程
date: 2013-10-25 08:14:00
updated: 2015-10-25 08:15:39
tags: 
- js
- css
categories: 
- js

---
    ps aux | grep -w Z # 列出整個 pid 的 process 信息
    ps aux | awk '{print $8 " " $2}' | grep -w Z # 列出 Z + pid
    ps aux | awk '$8=="Z" {print $2}' # 只抓出 pid