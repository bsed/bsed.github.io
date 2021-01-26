---
title: "linux下查看端口的占用情况 "
categories: [ "Linux" ]
tags: [ "linux","netstat" ]
draft: false
slug: "occupancy-of-linux-view-port"
date: "2012-03-09 10:52:00"
---

    netstat -tln
    wefs@Debian:~> netstat -tln
    --------------------------------------------------------------------------------
    Active Internet connections (only servers)
    Proto Recv-Q Send-Q Local Address           Foreign Address         State      
    tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN      
    tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN      
    tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN      
    tcp        0      0 0.0.0.0:5001            0.0.0.0:*               LISTEN      
    tcp        0      0 :::111                  :::*                    LISTEN      
    tcp        0      0 ::1:631                 :::*                    LISTEN      
    tcp        0      0 ::1:25                  :::*                    LISTEN      
    tcp        0      0 :::445                  :::*                    LISTEN      
    tcp        0      0 :::56362                :::*                    LISTEN      
    tcp        0      0 :::139                  :::*                    LISTEN     
    Netstat后面的意思


<!--more-->

*说明*：
·Proto显示连接使用的协议。 
·RefCnt表示连接到本套接口上的进程号。 
·Types显示套接口的类型。 
·State显示套接口当前的状态。 
·Path表示连接到套接口的其它进程使用的路径名
Netstat -tln 命令是用来查看linux的端口使用情况，
netstat -a 查看所有的服务端口
netstat -an | grep 端口号  来检验下是不是已经打开了某某端口
service xxx start 或者 /etc/init.d/xxx start是开启xxx某端口
iptables -A INPUT --dport xxx -j DROP
iptables -A OUTPUT --dport xxx -j DROP是关闭xxx端口