---
title: 在 mingw 安装 wget 插件
date: 2013-08-23 12:54:00
updated: 2015-02-03 16:03:47
tags: 
- meta
categories: 
- css

---
## 1. 设置mingw可在shell中输入和显示中 ##

中文显示

打开配置文件/etc/profile，在最后加入，

    export LANG=en
    
    alias l='/bin/ls --show-control-chars --color=auto'
    alias la='/bin/ls -aF --show-control-chars --color=auto'
    alias ll='/bin/ls -alF --show-control-chars --color=auto'
    alias ls='/bin/ls --show-control-chars --color=auto'

### 中文输入 ###


<!--more-->


将`/etc/inputrc.default`和用户目录下的`.inputrc`文件中的相关项修改为以下内容：

    set meta-flag on
    set input-meta on
    set output-meta on
    set convert-meta off

## 2. 安装wget软件 ##

由于mingw自带安装的wget版本过老，所以需要更新wget软件

    mingw-get update // 更新mingw的安装包信息
    mingw-get show | grep 'wget' // 显示wget安装包的相关信息
    mingw-get install msys-wget  // 安装最新版的wget安装包

## 3. 使用wget下载avlog中神话的所有聊天记录 ##

avlog.avplayer.org网站使用https协议，在wget中设置相关选项

    wget -r -nd -np --no-check-certificate 'https://avlog.avplayer.org/神话/'

运行后会发现wget不支持中文的传入，从wget的输出信息中可以发现，wget将url识别为

    https://avlog.avplayer.org/%C9%F1%BB%BO

这里很可能是因为mingw console是GBK编码的缘故，而wget又以unicode的方式去解析‘“神话”二字，最终导致wget connect的时候没有发现对应的文件（没印证过！）。对于wget不支持中文url的问题，现有两个解决方法，一种是修改源代码（google之，只需要简单修改几行代码就可以了），另一种方法是从浏览器的url栏拷贝url至gvim中，然后再将此转换后的url做为wget的url参数就可以了，如下所示。

    https://avlog.avplayer.org/%E7%A5%9E%E8%AF%9D

