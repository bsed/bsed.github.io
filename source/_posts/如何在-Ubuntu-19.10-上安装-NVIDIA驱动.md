---
title: "如何在 Ubuntu 19.10 上安装 NVIDIA驱动"
categories: [ "Linux" ]
tags: [ "drivers","nvidia" ]
draft: false
slug: "how-to-install-nvidia-driver-on-ubuntu-1910"
date: "2019-03-08 17:23:00"
---

## 禁用Nouveau开源驱动

在终端中

```bash
sudo vim /etc/modprobe.d/blacklist.conf
```

在文件中添加 blacklist nouveau 更新 initramfs

```bash
sudo update-initramfs -u
```

重启电脑


<!--more-->


## 清理原nvidia驱动

```
sudo apt autoremove --purge nvidia*
```

## 下载Nvidia Driver

<https://www.nvidia.cn/Download/index.aspx>


## 停止gdm

```
sudo service gdm stop
```

## 安装驱动

安装必要软件包

```bash
sudo apt install gcc make
```

ctrl+alt+f3进入虚拟控制台，登陆后

```bash
sudo ./NVIDIA-Linux-x86_64-XXX.XX.run
sudo apt install nvidia-prime nvidia-settings
reboot
```

## 解决画面撕裂

```bash
sudo vim /etc/modprobe.d/nvidia-blacklists-nouveau.conf
```

添加`options nvidia_drm modeset=1`

```bash
sudo update-initramfs -u
```