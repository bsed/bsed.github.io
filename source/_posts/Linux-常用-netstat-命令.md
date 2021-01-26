---
title: "Linux 常用 netstat 命令"
categories: [ "Linux" ]
tags: [ "linux","netstat" ]
draft: false
slug: "linux-commonly-used-netstat-commands"
date: "2016-11-21 20:52:00"
---

这是一个十分常用的命令，可以让你实时了解到云主机当前的状态，例如：常见的80连接数量，以及按一些排序，来检查是否被攻击？查看time_wait和syn连接数量等等。

## 1.查看所有80端口的连接数
```bash
netstat -nat|grep -i "80"|wc -l
```
## 2.对连接的IP按连接数量进行排序
```bash
netstat -ntu | awk '{print $5}' | cut -d: -f1 | sort | uniq -c | sort -n
```


<!--more-->


## 3.查看TCP连接状态
```bash
netstat -nat |awk '{print $6}'|sort|uniq -c|sort -rn
netstat -n | awk '/^tcp/ {++S[$NF]};END {for(a in S) print a, S[a]}' 
netstat -n | awk '/^tcp/ {++state[$NF]}; END {for(key in state) print key,"\t",state[key]}'
netstat -n | awk '/^tcp/ {++arr[$NF]};END {for(k in arr) print k,"\t",arr[k]}'
netstat -n |awk '/^tcp/ {print $NF}'|sort|uniq -c|sort -rn
netstat -ant | awk '{print $NF}' | grep -v '[a-z]' | sort | uniq -c
```
## 4.查看80端口连接数最多的20个IP
```bash
netstat -anlp|grep 80|grep tcp|awk '{print $5}'|awk -F: '{print $1}'|sort|uniq -c|sort -nr|head -n20
netstat -ant |awk '/:80/{split($5,ip,":");++A[ip[1]]}END{for(i in A) print A,i}' |sort -rn|head -n20
```
## 5.用tcpdump嗅探80端口的访问看看谁最高
```bash
tcpdump -i eth0 -tnn dst port 80 -c 1000 | awk -F"." '{print $1"."$2"."$3"."$4}' | sort | uniq -c | sort -nr |head -20
```
## 6.查找较多time_wait连接

netstat -n|grep TIME_WAIT|awk '{print $5}'|sort|uniq -c|sort -rn|head -n20
## 7.找查较多的SYN连接
```bash
netstat -an | grep SYN | awk '{print $5}' | awk -F: '{print $1}' | sort | uniq -c | sort -nr | more
```
