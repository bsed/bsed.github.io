---
title: "centos7 nginx Failed to read PID from file /run/nginx.pid: Invalid argument 解决方法"
categories: [ "日常" ]
tags: [ "nginx" ]
draft: false
slug: "centos7-nginx-failed-to-read-pid-from-file-runnginxpid-invalid-argument-solution"
date: "2017-11-08 13:14:00"
---

笔者在centos7上，配置nginx代理服务后， 
```bash
systemctl status nginx.service
```

提示错误 
Failed to read PID from file /run/nginx.pid: Invalid argument

看到好多说删掉改nginx.pid 文件的，试之，无效。


<!--more-->


后来找到了一个方法：
```bash 
mkdir -p /etc/systemd/system/nginx.service.d 
printf "[Service]\nExecStartPost=/bin/sleep 0.1\n" > /etc/systemd/system/nginx.service.d/override.conf
```
然后 
```bash
systemctl daemon-reload 
systemctl restart nginx.service
```