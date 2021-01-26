---
title: "Linux SSH远程传输命令 scp"
categories: [ "Linux" ]
tags: [ "linux","ssh","scp" ]
draft: false
slug: "ssh-linux-remote-transfer-command-scp"
date: "2016-11-05 21:15:00"
---

## 一、scp的用处

如果你有几台云主机，而阿里是支持内网传输的，并且不限制带宽，正常传输文件的速度能达到35m~50m/s，这个时候我们就可以使用内网，在几台云主机之间传输命令，只要把域名处，换成内网IP即可。使用的命令是scp，这个命令linux内置，本机和目标机，只要支持ssh，无须安装设置任何软件。

### 1、获取远程服务器上的文件
```bash
scp -P 2222 root@www.qcloud.com:/root/test.tar.gz /home/test.tar.gz
```
上端口大写P 为参数，2222 表示更改SSH端口后的端口，如果没有更改SSH端口可以不用添加该参数。 `root@www.qcloud.com` 表示使用root用户登录远程服务器www.legcloud.com，:/root/test.tar.gz 表示远程服务器上的文件，最后面的`/home/test.tar.gz`表示保存在本地上的路径和文件名。


<!--more-->


### 2、获取远程服务器上的目录
```bash
scp -P 2222 -r root@www.qcloud.com:/root/dirname/ /home/dirname/
```
### 3、将本地文件上传到服务器上
```bash
scp -P 2222 /home/test.tar.gz root@www.qcloud.com:/root/test.tar.gz
```
### 4、将本地目录上传到服务器上
```bash
scp -P 2222 -r /home/dirname/ root@www.qcloud.com:/root/dirname/
```