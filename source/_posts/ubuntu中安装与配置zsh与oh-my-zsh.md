---
title: "ubuntu中安装与配置zsh与oh-my-zsh"
categories: [ "Linux" ]
tags: [ "ubuntu","zsh" ]
draft: false
slug: "installation-and-configuration-of-zsh-and-ohmyzsh-in-ubuntu"
date: "2014-09-22 09:09:00"
---

# 1.ubuntu中默认安装了那些shell

    root@gnux:~# cat /etc/shells
    # /etc/shells: valid login shells
    /bin/sh
    /bin/dash
    /bin/bash
    /bin/rbash
    /bin/zsh

# 2.当前正在运行的是那个版本的shell


<!--more-->


`root@gnux:~# echo $SHELL/bin/bash`

# 3.安装zsh、git和wget：

`root@gnux:~# sudo apt-get install zsh git wget`

# 4.获取并自动按照oh-my-zsh：

    root@gnux:~#  wget --no-check-certificate https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh

# 5.替换bash为zsh：

    root@gnux:~# chsh -s /bin/zsh

设置zsh的参数：
安装完zsh后，在你的家目录下会有一个名为.zshrc的隐藏文件，可以 根据个人习惯配置zsh的参数，我就直接给出配置文件了
如有需要，请点击 这里 [下载](http://yunpan.cn/Q4kVfGvuBtGRG)
下载完毕，你将得到一个名为_.zshrc的文件
先进入家目录，备份原来的.zshrc,然后重命名_.zshrc文件: 

    root@gnux:~$ cp .zshrc .zshrc.bak
    root@gnux:~$ mv _.zshrc .zshrc

# 6.若谷出现chsh PAM 认证失败的话

    密码： 
    chsh: PAM: 认证失败

chsh实际上是更改/etc/passwd文件，在这个文件里面有一行是bin/bash，于是我用vi改为了bin/csh，保存。
然后重启系统。也实现了更改shell的目的。 

# 7.最后重启：

    root@gnux:~$ sudo reboot

重启后，完成。



