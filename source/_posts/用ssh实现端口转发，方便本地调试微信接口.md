---
title: "用ssh实现端口转发，方便本地调试微信接口"
categories: [ "Linux" ]
tags: [ "ssh" ]
draft: false
slug: "port-forwarding-with-ssh-to-facilitate-the-local-debugging-micro-channel-interface"
date: "2014-04-04 18:11:00"
---

端口转发有什么作用，目前来讲，我的理解是，可以实现本地端口请求的转发，不限定是哪种协议的请求。

设置一条端口转发大概格式是酱紫的：ssh -args portB:hostA:portA hostB 刚开始看到这个，我跟很多对linux命令行很不感冒的人一样，看到好几个类似的东西连在一块儿，分不清谁是谁。经过几番谷歌和实践，下面把我的学习所得写出来。

# 端口
从我上面写的格式可以看出来，第一个冒号前面的那个port永远是对应后面那个hostB，所以我把它写作portB。这样端口的对应关系一目了然。


<!--more-->


# 正向和反向
开始一直搞不清楚，什么是正向，什么是反向，也不知道什么时候该用正向，什么时候该用反向。后来我是通过这样理解来帮助记忆的：沿着书写命令行方向（从左到右）的数据流向就是正向，反之是反向。举个栗子，如果你把写命令写成ssh -fCNL portB:hostA:portA hostB,那这条命令的作用就是，你建立了这样一条tunnel连接：所有从hostA的portA端口的请求都会发到hostB的portB端口。仔细看这句话的逻辑和语序，是不是几乎完全是正（从左向右）序的？之所以说‘几乎’是因为还有个portB是始终要写在前面的。

那么，反之，如果你把命令行写成：ssh -fCNR portB:hostA:portA hostB，这条命令的作用就是，你建立了一条这样的tunnel连接：所有从hostB的portB端口走的请求都会被转发到hostA的portA端口上来。

# 参数
其实主要是正向参数L和反向参数R，其它一些参数的函数如下，请自行去理解或拓展学习：

    -f 后台运行
    
    -C 允许压缩数据
    
    -N 不执行任何命令
    
    -R 将端口绑定到远程服务器，反向代理
    
    -L 将端口绑定到本地客户端，正向代理