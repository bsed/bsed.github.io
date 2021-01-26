---
title: "windows 下 使用 MPV 播放器常用快捷键以及相关配置"
categories: [ "Linux" ]
tags: [ "windows","conf","mpv" ]
draft: false
slug: "using-mpv-player-and-related-configuration-in-windows"
date: "2020-01-04 20:12:00"
---

简介：MPV是一款适用于linux, mac!以及windows平台的核心向的开源播放器。
![mbioq_com_mpv-player-here.jpg][1]

mpv.exe——mpv的被编译出来的主程序
mpv.com——mpv的com接口
manual.pdf——英文使用说明书。

## 导出help文件：快捷键win+r，输入cmd。

在命令行窗口输入：cd /d “MPV根目录所在路径”  然后回车。再输入
mpv.com --list-options>help.txt 回车
可在根目录找到相应的help文件。


<!--more-->

## 快捷键
鼠标右键：暂停/播放；
鼠标滚轮上翻：快进；
鼠标滚轮下翻：快退

1：对比度-1(contrast-1)
2：对比度+1(contrast+1)
3：明度-1(brightness-1)
4：明度+1(brightness+1)
5：伽马曲线-1(gama-1)
6：伽马曲线+1(gama+1)
7：饱和度-1(saturation-1)
8:饱和度+1(saturation+1)
9：音量-1（volume-1）
0：音量+1（volume+1）
q：退出视频（quit）
w：(panscan-1)
e：(panscan+1)
r：字幕位置-1(sub position-1)
t：字幕位置+1(sub position+1)
u：强制使用ass字幕风格(ass subtitle style override)；
o：短期显示回放时间（osd）
p：暂停（pause）
s：截屏（sceenshot）
d：（drop frame）
f：全屏(full screen)
h：(tv channel)
j：顺序切换字幕（subject）
k：(tv channel)
l：AB区段循环(loop)
z：字幕延迟-100(sub delay-100)
x：字幕延迟+100(sub delay+100)
v：字幕显示/隐藏（subtitle visible）
m：静音(mute)
，：后退1帧
。：前进1帧

←：后退30s(按关键帧)
→：前进30s(按关键帧)
↑：前进60s(按关键帧)
↓：后退60s(按关键帧)
shift+←：后退1s
shift+→：前进1s
shift+↑：前进5s
shift+↓：后退5s

shift+1：上一章节（Previous chapter）
shift+2：下一章节(Next chapter) 
shift+3：切换音频（Switch audio）
shift+8：音量+2（volume+2）
shift+-：关闭视频，只留音频
shift+a：视频比例切换(video-aspect)
shift+d：启用/禁用反交错（deinterlace）
shift+i：视频信息（info）
shift+j：反序切换字幕（subject）
shift+o：显示回放时间（osd）
shift+q：退出视频（quit）


  [1]: https://imgs.gnux.cn/usr/uploads/2020/01/4132561088.jpg