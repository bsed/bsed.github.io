---
title: "Debian 9/Ubuntu 17+添加rc.local开机自启的方法"
categories: [ "Linux" ]
tags: [ "linux","ubuntu","debian","rc.local" ]
draft: false
slug: "debian-9ubuntu-17-adding-rclocal-to-boot-self-start-method"
date: "2018-10-17 17:24:00"
---

**说明**：很多时候有些程序或者脚本都需要添加开机自启，最简单的方法就是使用rc.local自启，不过由于系统版本更替，很多新版本系统都没有rc.local文件了，比如Debian 9、Ubuntu 17.10、Ubuntu 18.04。这时候就需要我们手动设置下。
方法

## 1、添加rc-local.service


<!--more-->


```bash
#以下为一整条命令，一起复制运行
cat > /etc/systemd/system/rc-local.service <<EOF
[Unit]
Description=/etc/rc.local
ConditionPathExists=/etc/rc.local
 
[Service]
Type=forking
ExecStart=/etc/rc.local start
TimeoutSec=0
StandardOutput=tty
RemainAfterExit=yes
SysVStartPriority=99
 
[Install]
WantedBy=multi-user.target
EOF
```
## 2、新建rc-local文件
```bash
#以下为一整条命令，一起复制运行
cat > /etc/rc.local <<EOF
#!/bin/sh -e
#
# rc.local
#
# This script is executed at the end of each multiuser runlevel.
# Make sure that the script will "exit 0" on success or any other
# value on error.
#
# In order to enable or disable this script just change the execution
# bits.
#
# By default this script does nothing.
 
exit 0
EOF
```
## 3、添加权限并设置开机自启
```bash
chmod +x /etc/rc.local
systemctl enable rc-local
systemctl start rc-local.service
```
检查状态：

`systemctl status rc-local.service`

返回`Active:active`信息，则成功。

最后我们就可以在`/etc/rc.loacl`里，添加开机的自启命令什么的了。记住添加在exit 0之前。