---
title: "ubuntu 优化技巧"
categories: [ "Linux" ]
tags: [ "ubuntu","chrome" ]
draft: false
slug: "ubuntu-optimization-techniques"
date: "2014-10-13 20:22:00"
---

## 转移chrome缓存

最好的就是关机自动清除缓存，懒人必备，利用ramdisk实现，`vi /etc/fstab`添加
```bash
tmpfs /dev/shm tmpfs defaults,size=512M 0 0
```
然后退出chrome，建立硬链接。
```bash
sudo rm -rf ~/.cache/google-chrome
sudo ln -s /dev/shm ~/.cache/google-chrome
```
测试chrome是否成功：`chrome:cache`


<!--more-->


##创建AS快捷方式

以AS为例，这是自己创建的Android Studio.desktop文件内容
```
[Desktop Entry]
Version=1.0
Type=Application
Name=Android Studio
Exec="/home/kelvin/tools/android-studio/bin/studio.sh" %f
Icon=/home/kelvin/tools/android-studio/bin/studio.png
Categories=Development;IDE;
Terminal=false
StartupNotify=true
StartupWMClass=jetbrains-android-studio
Name[en_GB]=android-studio.desktop
```
可以根据实际，修改图标和程序执行位置。
更改为可执行权限
```
chmod a+x 'Android Studio.desktop'
```
对于GNOME3桌面环境，可将其放置在系统托盘的快捷方式中，将`Android Studio.desktop`复制到`/usr/share/applications`下面即可。