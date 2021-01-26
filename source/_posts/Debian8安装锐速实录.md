---
title: "Debian8安装锐速实录"
categories: [ "Linux" ]
tags: [ "linux","debian","bash" ]
draft: false
slug: "debian8-installation-sharp-record"
date: "2015-09-15 11:37:00"
---

完全采用Debian8发行版
## 安装3.12-1-amd64内核
由于Debian官方不再支持3.12lts内核，只能通过snapshot做变通的解决方法。

```bash
wget http://snapshot.debian.org/archive/debian/20140310T221406Z/pool/main/l/linux/linux-image-3.12-1-amd64_3.12.9-1_amd64.deb
wget http://snapshot.debian.org/archive/debian/20140310T221406Z/pool/main/l/linux/linux-headers-3.12-1-common_3.12.9-1_amd64.deb
dpkg -i *.deb
```
安装完成重启时请自行选择内核，删除原内核

```bash
apt remove linux-image-amd64
```
## 安装锐速

```bash
wget -N --no-check-certificate https://raw.githubusercontent.com/91yun/serverspeeder/master/serverspeeder-all.sh && bash serverspeeder-all.sh
```
## 卸载锐速

```
chattr -i /serverspeeder/etc/apx* && /serverspeeder/bin/serverSpeeder.sh uninstall -f
```