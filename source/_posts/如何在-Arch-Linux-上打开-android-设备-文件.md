---
title: "如何在 Arch Linux 上打开 android 设备 文件"
categories: [ "Linux" ]
tags: [ "android","archlinux","fusermount","mtp" ]
draft: false
slug: "how-to-open-android-device-files-on-arch-linux"
date: "2019-09-23 19:45:00"
---

## 步骤 
### 安装`libmtp`,使用 `mtp-detect`查看是否能检测到mtp设备：
```bash
sudo pacman -Sy libmtp  
sudo mtp-detect  
```


<!--more-->


### 安装`simple-mtpfs`,并mount设备
```bash
sudo pacman -Sy simple-mtpfs  
sudo mkdir /mnt/mtp  
sudo simple-mtpfs -o allow_other /mnt/mtp  
```
### 卸载设备：
```bash
sudo fusermount -u /mnt/mtp
```