---
title: "i3-wm 配置 HiDPI"
categories: [ "Linux" ]
tags: [ "i3-wm","hidpi" ]
draft: false
slug: "i3wm-configuration"
date: "2019-05-19 11:40:00"
---

我在  xubuntu 上安装 i3 平铺管理器，我需要HiDPI 模式。

## 环境
xubuntu 19.04
i3 4.16.1 (a.k.a. i3-wm)
4k single monitor

## 问题
如何在 i3-wm 上 正确配置HiDPI 模式呢?

<!--more-->

##  解决方案 

将以下行写入文件 , `~/.Xresources`.  DPI值可以根据需要进行调整。 
```bash
Xft.dpi: 144
```
 确保文件已加载到 ` ~/.xinitrc`.  例如，我的默认`.xinitrc` 文件如下所示。 
```bash
#!/bin/bash
#
# ~/.xinitrc
#
# Executed by startx (run your window manager from here)
	
userresources=$HOME/.Xresources
usermodmap=$HOME/.Xmodmap
sysresources=/etc/X11/xinit/.Xresources
sysmodmap=/etc/X11/xinit/.Xmodmap
	
SESSION=${1:-xfce}
	
# merge in defaults and keymaps
	
if [ -f $sysresources ]; then
	xrdb -merge $sysresources
fi
	
if [ -f $sysmodmap ]; then
	xmodmap $sysmodmap
fi
	
if [ -f "$userresources" ]; then
	xrdb -merge "$userresources"
fi
	
if [ -f "$usermodmap" ]; then
	xmodmap "$usermodmap"
fi
	
# start some nice programs
	
if [ -d /etc/X11/xinit/xinitrc.d ] ; then
	for f in /etc/X11/xinit/xinitrc.d/?*.sh ; do
		[ -x "$f" ] && . "$f"
	done
	unset f
fi
	
get_session(){
	local dbus_args=(--sh-syntax --exit-with-session)
	case "$SESSION" in
		awesome) dbus_args+=(awesome) ;;
		bspwm) dbus_args+=(bspwm-session) ;;
		budgie) dbus_args+=(budgie-desktop) ;;
		cinnamon) dbus_args+=(cinnamon-session) ;;
		deepin) dbus_args+=(startdde) ;;
		enlightenment) dbus_args+=(enlightenment_start) ;;
		fluxbox) dbus_args+=(startfluxbox) ;;
		gnome) dbus_args+=(gnome-session) ;;
		i3|i3wm) dbus_args+=(i3 --shmlog-size 0) ;;
		jwm) dbus_args+=(jwm) ;;
		kde) dbus_args+=(startkde) ;;
		lxde) dbus_args+=(startlxde) ;;
		lxqt) dbus_args+=(lxqt-session) ;;
		mate) dbus_args+=(mate-session) ;;
		xfce) dbus_args+=(xfce4-session) ;;
		openbox) dbus_args+=(openbox-session) ;;
		*) dbus_args+=("$SESSION") ;;
	esac
	
	echo "dbus-launch ${dbus_args[*]}"
}
	
exec $(get_session)
```

 重新启动系统并确认显示结果。 
