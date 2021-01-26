---
title: "如何解决linux下dpkg: error processing ess"
categories: [ "Linux" ]
tags: [ "ubuntu" ]
draft: false
slug: "how-to-solve-dpkg-linux-error-processing-ess"
date: "2015-07-20 20:28:00"
---

```bash
emacsen-common: Handling install of emacsen flavor emacs24
/usr/lib/emacsen-common/packages/install/emacsen-common: line 29: emacs24: command not found
ERROR: install script from emacsen-common package failed
dpkg: error processing package ess (--configure):
 subprocess installed post-installation script returned error exit status 1
Errors were encountered while processing:
 ess
E: Sub-process /usr/bin/dpkg returned an error code (1)
```
**解决办法**： 


<!--more-->


1.将info文件夹更名：`sudo mv /var/lib/dpkg/info/ /var/lib/dpkg/info_old/`
2.再新建一个新的info文件夹 ：`sudo mkdir /var/lib/dpkg/info/`
3.apt-get update：`sudo apt-get update` 
4.重新安装 ：`sudo apt-get -f install`
5.执行完上一步操作后会在新的info文件夹下生成一些文件，现将这些文件全部移到info_old文件夹下 ：`sudo mv /var/lib/dpkg/info/* /var/lib/dpkg/info_old/` 
6.把自己新建的info文件夹删掉 ：`sudo rm -rf /var/lib/dpkg/info` 
7.把info_old文件夹重新改回info ：`sudo mv /var/lib/dpkg/info_old/ /var/lib/dpkg/info/` 
8.重新安装需要安装的软件 
```
sudo apt-get update
sudo apt-get -f install
```
