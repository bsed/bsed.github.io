---
title: "20-multi-gpu-setup.conf"
categories: [ "日常" ]
tags: [ "nvidia" ]
draft: false
slug: "20multigpusetupconf"
date: "2019-06-28 13:21:00"
---

ubuntu nvidia gpu:


<!--more-->


```
# /etc/X11/xorg.conf.d/20-multi-gpu-setup.conf

Section "ServerLayout"
    Identifier "layout"
    Screen 0 "intel"
    Screen 1 "nvidia1"
    Screen 2 "nvidia2"
EndSection

Section "Device"
    Identifier "intel"
    Driver "intel"
    BusID "PCI:0:2:0"
EndSection

Section "Screen"
    Identifier "intel"
    Device "intel"
EndSection

Section "Device"
    Identifier "nvidia1"
    Driver "nvidia"
    BusID "PCI:1:0:0"
EndSection

Section "Screen"
    Identifier "nvidia1"
    Device "nvidia1"
EndSection

Section "Device"
    Identifier "nvidia2"
    Driver "nvidia"
    BusID "PCI:2:0:0"
EndSection

Section "Screen"
    Identifier "nvidia2"
    Device "nvidia2"
EndSection
```