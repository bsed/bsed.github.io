---
title: "ubuntu16.10 下载安装以及移除 qt5.7"
categories: [ "Linux" ]
tags: [ "ubuntu","qt" ]
draft: false
slug: "ubuntu1610-download-installation-and-unloading-qt57"
date: "2015-09-15 09:36:00"
---

## 安装：

1. Qt5.3.1下载地址为：http://qt-project.org/，选择”[Qt 5.7.1](http://download.qt.io/official_releases/qt/5.7/5.7.0/qt-opensource-linux-x64-5.7.0.run)”版本，文件名是"qt-opensource-linux-x64-5.7.0.run"；

2. 进入`qt-opensource-linux-x64-5.7.0.run`目录下，修改文件权限：`chmod u+x qt-opensource-linux-x64-5.7.0.run`；


<!--more-->


3. 打开安装界面： `./qt-opensource-linux-x64-5.7.0.run`；

4.图形化界面安装；

5.select all（选择全部）；

6.选择LGPL协议，安装即可；

7.启动Qt Creater：进入`Qt5.7.0/Tools/QtCreater/bin/`，可以鼠标双击qtcreater启动；

8.如果你的linux中已经安装了opengl库，则不需要以下命令，否则运行以下命令安装opengl库：sudo apt-get install freeglut3-dev；

9.建立Qt5.37.0桌面快捷方式（主要注意解释的三行代码即可）：

  （1）在桌面上建立Qt5.7.0.desktop文件，将以下代码复制进去：
```
[Desktop Entry]
Categories=Development;
Comment[zh_CN]=
Comment=
Exec=/home/kelvin/Qt5.7.0/Tools/QtCreater/bin/qtcreator  解释：这里放的是qtcreator的具体文件路径
GenericName[zh_CN]=IDE
GenericName=IDE
Icon=/home/kelvin/Pictures/Qt.png  解释：这里放的是桌面快捷方式的图标路径
MimeType=
Name[zh_CN]=Qt5.7.0   解释：这里放的是桌面快捷方式的名称
Name=Qt5.7.0 
Path=
StartupNotify=true
Terminal=false
Type=Application
X-DBUS-ServiceName=
X-DBUS-StartupType=
X-KDE-SubstituteUID=false
X-KDE-Username=warsllon
```
（2）进入Qt5.7.0.desktop文件的目录中：`cd /home/kelvin/Desktop`
（3）赋予该文件权限：`chmod +x Qt5.7.0.desktop`
（4）桌面快捷方式创建完毕。

## 卸载：

1.进入Qt5.7.0的安装目录：例如我的目录：cd /home/warsllon/SoftWare/Qt5.7.0；

2.运行命令：./MaintenanceTool；

3.进入图形化界面，按照操作卸载即可。

