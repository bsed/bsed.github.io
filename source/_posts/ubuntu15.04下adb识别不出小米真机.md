---
title: "ubuntu15.04下adb识别不出小米真机"
categories: [ "Android" ]
tags: [ "ubuntu","adb" ]
draft: false
slug: "ubuntu1504-adb-not-recognize-millet-real-heroes-who-knows"
date: "2014-09-29 10:14:00"
---

 手机拨号输入 `*#*#717717#*#*` 打开端口

`/etc/udev/rules.d/51-Android.rules` 里追加


<!--more-->


ATTR{idVendor}=="2717", ATTR{idProduct}=="1368", MODE=”0666"
#我小米是M2 “05c6”根据连接手机  lsusb 的 ID 修改 (Bus 002 Device 027: ID 05c6:9031 Qualcomm, Inc.)

    sudo chmod a+rx /etc/udev/rules.d/50-android.rules
    
    sudo service udev restart
    
    adb kill-server
    
    adb start-server

运行; adb devices

root@gnux:~# adb devices
List of devices attached 
50988f53	device


