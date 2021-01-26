---
title: "ubuntu15.04 以及以上 删除不必要的软件包 ，安装搜狗输入法"
categories: [ "Linux" ]
tags: [ "ubuntu","sogou" ]
draft: false
slug: "ubuntu1504-install-sogou-input-method"
date: "2015-11-03 21:01:00"
---

 首先:打开系统设置->软件和更新,添加以下源.

    deb http://archive.ubuntukylin.com:10006/ubuntukylin trusty main


然后

    sudo apt-get update # error  see after
    sudo apt-get install sogoupinyin

注销或重启系统后熟悉的搜狗输入法就出现了.
结合网络上的信息和我个人需要写的摘记
0.更新源

## 加源方法：

    sudo gedit /etc/apt/sources.list

## 更新源：


<!--more-->


    sudo apt-get update

## 更新软件：

    sudo apt-get upgrade

阿里云源：

    deb http://mirrors.aliyun.com/ubuntu/ vivid main restricted universe multiverse

    deb http://mirrors.aliyun.com/ubuntu/ vivid-security main restricted universe multiverse

    deb http://mirrors.aliyun.com/ubuntu/ vivid-updates main restricted universe multiverse

    deb http://mirrors.aliyun.com/ubuntu/ vivid-proposed main restricted universe multiverse

    deb http://mirrors.aliyun.com/ubuntu/ vivid-backports main restricted universe multiverse

    deb-src http://mirrors.aliyun.com/ubuntu/ vivid main restricted universe multiverse

    deb-src http://mirrors.aliyun.com/ubuntu/ vivid-security main restricted universe multiverse

    deb-src http://mirrors.aliyun.com/ubuntu/ vivid-updates main restricted universe multiverse

    deb-src http://mirrors.aliyun.com/ubuntu/ vivid-proposed main restricted universe multiverse

    deb-src http://mirrors.aliyun.com/ubuntu/ vivid-backports main restricted universe multiverse

ubuntu kylin源：

    deb http://archive.ubuntukylin.com:10006/ubuntukylin trusty main

注：如果提示公钥错误，可以按以下方式解决：

    sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 6AF0E1940624A220 #此处6AF0E1940624A220需要是错误提示的key

添加PPA源（PPA：Personal Package Archives，个人软件仓库）

如果提示hash 校验和不符，有可能是网络不好引起的

    #为了安装SysPeek

    sudo add-apt-repository ppa:nilarimogard/webupd8

    #为了安装apt-fast

    sudo add-apt-repository ppa:tldm217/tahutek.net

1.删除libreoffice

libreoffice虽然是开源的，但是Java写出来的office执行效率实在不敢恭维，装完系统后果断删掉

        sudo apt-get remove libreoffice-common  


2.删除Amazon的链接

    sudo apt-get remove unity-webapps-common  


3.删掉基本不用的自带软件（用的时候再装也来得及）

    sudo apt-get remove thunderbird totem rhythmbox empathy brasero simple-scan gnome-mahjongg aisleriot gnome-mines cheese transmission-common gnome-orca webbrowser-app gnome-sudoku  landscape-client-ui-install onboard deja-dup

    备注：

    thunderbird：Mozilla浏览器的邮件功能部件所改造的邮件工具

    totem：影片播放器

    rhythmbox：音乐播放和管理软件

    empathy：即时通讯工具

    brasero：光盘烧录软件

    simple-scan：扫描工具

    gnome-mahjongg：麻将游戏--对对碰

    aisleriot：纸牌游戏

    gnome-mines：扫雷游戏

    cheese：摄像头拍照和录像软件cheese

    transmission-common：BT客户端

    gnome-orca：屏幕阅读器

    webbrowser-app：系统自带web浏览器

    gnome-sudoku：数独游戏

    landscape-client-ui-install：远程管理软件

    onboard：虚拟键盘

    deja-dup：备份工具

这样系统就基本上干净了。下面几个根据自己需要来弄


4.安装Vim

居然默认没有集成Vim神器，只能手动安装了。

    sudo apt-get install vim  


5.安装搜狗输入法

    sudo apt-get install sogoupinyin  


7.安装系统指示器SysPeek

     sudo apt-get install syspeek    


8.Apt-fast

是一个用 axel 来加速 apt-get 软件安装的脚本,由于是多线程下载，所以加速效果还是很明显的。

    sudoapt-get installapt-fast

使用方法:

    用apt-fast 命令替代原apt-get 命令,如：

    apt-fast updateapt-fastinstallfcitx

9.Silentcast

这是一个gif动画录制软件，后缀可以是gif, webm,mp4 ，支持全屏，窗口，某个可选区域。

    sudo add-apt-repository ppa:sethj/silentcast  
    
    sudo apt-get update
    
    sudo apt-get install silentcast

10.netspeed

显示网络下载速度

    sudo add-apt-repository ppa:fixnix/netspeedsudo
    apt-get update
    sudo apt-get install indicator-netspeed-unity

Dash下搜索 `Startup App` 将此软件加入开机启动项目. 

11.wmail

由于对接的是gmail邮箱(gfw)，安装见下面链接 建议有vpn帐号的可以试用下.

[http://tipsonubuntu.com/2016/07/26/gmail-client-wmail-ubuntu-16-04/](http://tipsonubuntu.com/2016/07/26/gmail-client-wmail-ubuntu-16-04/)

12.Shutter

ubuntu 下不错的截图软件 

    sudo add-apt-repository -y ppa:shutter/ppa
    sudo apt-get update
    
    sudo apt-get install shutter

13. GIMP（非必须的）

ubuntu 下的图像处理 工具 

    sudo add-apt-repository ppa:otto-kesselgulasch/gimp-edge
    sudo apt update && sudo apt install gimp gimp-gmic
    sudo apt install ppa-purge && sudo ppa-purge ppa:otto-kesselgulasch/gimp-edge

14. ClipGrab（非必须）

下载 YouTube, Vimeo Videos 视频

    sudo add-apt-repository ppa:clipgrab-team/ppa
    sudo apt update
    sudo apt install clipgrab

15. Nixnote （非必须）
 
linux 下的  evernote 笔记

    sudo add-apt-repository ppa:nixnote/nixnote2-daily
    sudo apt update; sudo apt install nixnote2
    sudo apt remove nixnote2 && sudo apt autoremove

16. Nylas N1 Email Client 

linux 下 不错的 邮件客户端

    wget https://edgehill.s3-us-west-2.amazonaws.com/0.4.49-c9c04b7/linux-deb/x64/N1.deb
    
    wget https://edgehill.s3-us-west-2.amazonaws.com/0.4.49-c9c04b7/linux-rpm/x64/N1.rpm

    sudo dpkg -i N1.deb
    sudo dnf install N1.rpm

**CentOS**

    sudo yum install N1.rpm

**OpenSUSE**

    sudo zypper in N1.rpm

**Arch Linux users can install it from AUR with Yaourt package manager.**

    yaourt n1

详细链接: [https://www.linuxbabe.com/desktop-linux/nylas-n1-ubuntu-16-04-debian-8-fedora-opensuse-arch](https://www.linuxbabe.com/desktop-linux/nylas-n1-ubuntu-16-04-debian-8-fedora-opensuse-arch)

17. TLP

ubuntu 下的电池管理软件

    sudo apt install tlp tlp-rdw tp-smapi-dkms acpi-call-dkms
    sudo add-apt-repository ppa:linrunner/tlp
    
    sudo apt update && sudo apt install tlp tlp-rdw tp-smapi-dkms acpi-call-dkms

    sudo tlp start
    #sudo tlp-stat

详细链接: [https://www.linuxbabe.com/ubuntu/tlp-linux-laptop-battery-life](https://www.linuxbabe.com/ubuntu/tlp-linux-laptop-battery-life)


18. TimeShift

创建系统重置点,冰点还原

    sudo apt-add-repository -y ppa:teejee2008/ppa
    sudo apt update; sudo apt install timeshift


