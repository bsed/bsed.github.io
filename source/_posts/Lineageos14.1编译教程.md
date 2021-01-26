---
title: "Lineageos14.1编译教程"
categories: [ "Android" ]
tags: [ "android","lineageos" ]
draft: false
slug: "lineageos141-compiler-tutorial"
date: "2017-04-15 20:30:00"
---

有这篇帖子主要是大神的教程大都是5.1.1及之前的教程，，不适合现在的7.1.1。
所以自己根据 @dlhxr 的5.1.1和lineagos官方文档总结，希望给喜欢编译的人明确指导的方案。只要你严格按照我的步骤，不会出错的

源码下载需要翻墙，请按照此贴翻墙
https://laod.cn/hosts/2017-google-hosts.html

首先感谢玩机组各成员的努力付出，希望各位加油齐心协力，共筑美好一加社区


本帖隐藏的内容
## 一、环境搭建
1、安装64位Ubuntu14.04系统（实体安装、虚拟机安装均可）

注意：要求机器至少8G内存，建议16G。（虚拟机至少分配8G内存），如果实体机只有8G又想编译，请将虚拟内存（swap）设置8G，这样实体8G+虚拟8G=16G（安装Ubuntu时设置，具体百度），我这里分了一个盘大概200G，系统加源码，剩余可用空间在130G，，请分配好空间。
终端调用是（同时按下键盘Ctrl+Alt+T）
2、更新系统至最新版本，在终端下输入

    sudo apt-get update
    sudo apt-get upgrade


3、安装编译必需软件包  

    sudo apt-get install bison build-essential curl flex git gnupg gperf libesd0-dev
    libncurses5-dev libsdl1.2-dev libwxgtk2.8-dev libxml2 libxml2-utils lzop
    openjdk-8-jdk openjdk-8-jre pngcrush schedtool squashfs-tools xsltproc zip
    zlib1g-dev g++-multilib gcc-multilib lib32ncurses5-dev lib32readline-gplv2-dev
    lib32z1-de



Ubuntu 16.04 及以上系统, 替换:


    libwxgtk2.8-dev → libwxgtk3.0-dev

注：无法安装JDK8（方法见下面网址）

    sudo apt-get install openjdk-8-jre openjdk-8-jdk


网址： http://ubuntuhandbook.org/index.php/2015/01/install-openjdk-8-ubuntu-14-04-12-04-lts/


4、建立repo命令

    mkdir -p ~/bin
    curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
    chmod a+x ~/bin/repo
    echo "export PATH=~/bin:$PATH" >> ~/.bashrc

将 ~/bin 加进path 环境中
你可以检查以下代码是否在 .profile，如果不在请添加:
（注意： .profile 或 .bashrc 在HOME目录下按 Ctrl+ H 打开隐藏文件）

    # set PATH so it includes user's private bin if it exists
    if [ -d "$HOME/bin" ] ; then
        PATH="$HOME/bin:$PATH"
    fi


然后运行 source ~/.profile 来更新环境。


## 二、安装SDK
如果你没安装 adb 和 fastboot工具, 你可以从这里下载。将下载的文件解压到home下
安装lineageos指定SDK adb 和 fastboot工具
链接：http://pan.baidu.com/s/1dEAwJ8d 密码：1yeq

现在添加 adb 和 fastboot 到我们的path。打开 ~/.profile 添加如下代码：

    # add Android SDK platform tools to path
    if [ -d "$HOME/platform-tools" ] ; then
        PATH="$HOME/platform-tools:$PATH"
    fi

然后终端运行 source ~/.profile 来更新我们的环境变量

## 三、建立LineageOS目录并同步源码和依赖
1、建立机型目录并进入

    mkdir ~/LineageOS && cd ~/LineageOS


2、设置的你Github用户名和邮箱（提前注册github账户）

    git config --global user.email "你的邮箱"
    git config --global user.name "你的用户"

3、安装Lineageos的repo

    repo init -u git://github.com/LineageOSy/android.git -b cm-14.1


4、同步代码

    repo sync -f --force-sync--no-clone-bundle


注意同步的代码大概有25GB左右，需要4小时甚至更多，看你的网速了。如果你的网速较慢，可能会同步失败，如果失败就再次执行$repo sync，会继续下载，直到完成，可断点续传


5、同步完成后执行

这步是同步你的手机相关代码，不过完成后需要加入你机器的厂商vendor文件，可以从手机上获取，
手机获取文件方法：
将你的oneplus 3连接上电脑并打开 USB 开发者选项, 同意开启的弹窗
进入目录：

    cd ~lineageos/android/system/device/oneplus/oneplus3

复制代码

然后终端运行

命令：

    ./extract-files.sh


也可以在这里找到https://github.com/TheMuppets
按照下面格式(name=”github路径” path=”本地路径”)把他加入
~/LineageOS/.repo/local_manifests/roomservice.xml中，之后输入命令：

    repo sync

就会把这些文件同步到vendor/oneplus文件夹中



## 四、编译前
1、编译前准备

先设定缓存加快编译、方便下次编译提升速度
终端输入：

    export USE_CCACHE=1

复制代码
添加到 ~/.bashrc
然后：

    prebuilts/misc/linux-x86/ccache/ccache -M 50G


    export CCACHE_COMPRESS=1

添加到 ~/.bashrc

我之前一直编译错误，Google后发现是JACK默认限制使用的内存太小（来源），所以先执行

    export ANDROID_JACK_VM_ARGS="-Dfile.encoding=UTF-8 -XX:+TieredCompilation -Xmx4G"


2、出错解决
如果编译前没有执行上面export命令，编译出错后再执行export，需要用下面命令重启jack

    out/host/linux-x86/bin/jack-admin kill-server
    out/host/linux-x86/bin/jack-admin start-server


## 五、编译
1、终端进入
lineageos源码目录

    cd ~/LineageOS
    . build/envsetup.sh
    brunch oneplus3


之后将开始编译程序，请耐心等待，大概一加三编译时间在2小时。（不同机型时间不同）
如果编译成功，终端会出现一行绿色的字。
Zip包可以在

    ~/LineageOS/out/target/product/


中找到；将编译的包复制到手机就可以刷了，同时会生成md5校验文件，可以一同复制，防止出错


下次编译前请清理上次的编译文件，在lineagos目录下输入：

    make clobber

再次按执行编译代码编译
注：
LineageOS默认取消了ROOT，如果希望编译的ROM自带ROOT，需要加入环境变量

    export WITH_SU=true


原文：http://www.oneplusbbs.com/thread-3334576-1-1.html