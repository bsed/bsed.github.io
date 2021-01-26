---
title: "利用 xrandr 命令修改屏幕分辨率"
categories: [ "Linux" ]
tags: [ "ubuntu","eDP-1" ]
draft: false
slug: "modifying-the-screen-resolution-with-the-xrandr-command"
date: "2018-04-21 10:30:00"
---

## 问题背景
操作系统：ubuntu 18.04
电脑外接着一个 14‘ 的显示器，分辨率为 1024*768。
今天，通过 Setting->Displays 中选择外接屏的分辨率时，发现最，并且外接屏显示 “Unknown Displays”，

## 解决方案
解决办法主要是给这个 “Unknown Displays” 设置一个 1920*1080 的选项，下面借助 xrandr 命令来实现。
先查看两个屏幕支持的分辨率情况

<!--more-->


在终端输入 xrandr：
```bash
$ xrandr
Screen 0: minimum 320 x 200, current 2240 x 1024, maximum
eDP-1 connected 960x540+0+0 (normal left inverted right x axis 
   960x540       59.82* 
   864x486       59.92    59.57  
   640x480       59.94  
   480x270       59.82  
   432x243       59.92    59.57  
   320x240       60.05  
HDMI-1 connected 1280x1024+960+0 (normal left inverted right x 
   1280x1024     60.02*+  75.02    72.05  
   1920x1080     60.00    59.94  
   1152x864      75.00  
   1280x720      60.00    59.94  
   1024x768      75.03    70.07    60.00  
   832x624       74.55  
   800x600       72.19    75.00    60.32  
   720x480       60.00    59.94  
   640x480       75.00    72.81    66.67    60.00    59.94  
   720x400       70.08  
```
注：
上面显示了两个屏幕分辨率选项，后面标示 * 即为屏幕当前分辨率
这里显示好几个接口：eDP1, DP1, HDMI1, DP2, HDMI2, VIRTUAL1，但是只有 eDP1 和 HDMI-1 有连接，并且 HDMI-1 对应我们外接屏（这个值后面会用到！）

## 利用 cvt 新建一个 modeline

代码如下：
```bash
~  cvt 1920 1080
```
然后屏幕上会返回两行内容，赋值第二行中 ‘Modeline’ 后面的所有内容，并接到下面 xrandr --newmode 后面：
```bash
~  xrandr --newmode "1920x1080_60.00" 173.00  1920 2048 2248 2576  1080 1083 1088 1120 -hsync +vsync
~  xrandr --addmode eDP1 "1920x1080_60.00"
```
```bash
其中 ”HDMI-1“ 即上面展示的外接端口，不用的接口这个名字可能不同，比如有的会是 VGA1，以上面 xrandr 的显示结果为准。

之后，再进入 Setting->Displays, 发现那个 “Unknown Display” 的分辨率中，有了 “1920x1080” 这个选项，选中它，并 Apply 即可。