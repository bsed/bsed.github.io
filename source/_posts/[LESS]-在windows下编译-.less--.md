---
title: "[LESS] 在windows下编译*.less  "
categories: [ "JS" ]
tags: [ "windows","less" ]
draft: false
slug: "windows-less-compile"
date: "2014-11-08 19:40:00"
---

这篇不介绍 [LESS](http://lesscss.org/) 是甚么,只记录一下怎么准备在 windows 下用 command-line 编译 *.less 的环境.

<!--more-->

#### 方式一：####

 1. 安装 [node.js](http://nodejs.org/)

直接从 node.js 网站下载msi安装档后执行安装即可. 

 2. 透过 [npm](http://npmjs.org/) 安装 less

npm是node.js的package manaer,安装完 node.js 后就会这程式可以用. 
所以只要透过npm安装less这个package就可以了. 
首先执行`node.js command prompt`,在开启的command视窗下执行 

    npm install less -g 

LESS官方网站上说明是是直接使用 npm install less,不过为了方便使用lessc, 
所以建议加上-g, 将它安装成global package 
执行完指令后,就等它跑完就可以使用lessc了 

 3. 使用 lessc 编译*.less

在node.js command prompt的视窗下先切换到你要编译的 *.less 档案目录下, 
然后执行下面指令 

    lessc filename.less > filename.css 

执行完后,指定的css档就会产生在所在的目录下

#### 方式二 ####

1、安装node.js:[http://nodejs.org/download/](http://nodejs.org/download/)

在命令行中直接运行node -v,命令行将打印出:v0.6.1,表示安装成功。

2、安装GIT工具，[https://code.google.com/p/msysgit/downloads/list](https://code.google.com/p/msysgit/downloads/list)

3、安装NPM，git执行以下命令

    git clone --recursive git://github.com/isaacs/npm.git 
    cd npm 
    node cli.js install npm -gf 

命令行运行npm install underscore，返回类似underscore@1.2.2 ./node_modules/underscore ，表示安装成功

4、安装less,git运行npm install -g less@latest，
运行less例：

    lessc --compress all.less > all.min.css
    lessc all.less > all.css

引者注：
需要在系统的路径变量 path 加上node所在目录。否则无法命令行调用。

#### 如果以上方法安装失败：####
说明node 有问题了
*请参考第三种方法*

答案是在这里找到的：[install in windows](https://github.com/joyent/node/wiki/Installation#installing-on-windows)，下面简要的说一下安装步骤：

 1. 下载nodejs引擎，[32bit version](http://nodejs.org/dist/latest/node.exe)或者[64bit version](http://nodejs.org/dist/latest/x64/node.exe)
 2. 下载最新版的npm  zip格式压缩包：[http://nodejs.org/dist/npm/](http://nodejs.org/dist/npm/)
 3. 在硬盘某个位置，如D盘下建立一个文件nodejs，把上面两个下载的东西都放在这里，npm要解压。
 4. 配置两个环境变量：一个是PATH上增加node.exe的目录D:nodejs,一个是增加环境变量NODE_PATH,值为D:nodejsnode_modules。
 5. win7环境配置在系统》高级系统设置》高级》环境变量》系统变量中查找PATH，编辑加上D:nodejs，再加上NODE_PATH变量和值。
 6. 安装express：在Node目录下键入指令`npm install express`[安装express至相对路径]或`npm
    install express -g`[安装express至绝对路径]

到这里，你在命令行里面输入node -v如果输出nodejs的版本则安装成功。

*附录：*

### 安装git ###

1 官方网站 http://msysgit.github.io/ 点击下载 [https://code.google.com/p/msysgit/downloads/list?q=full+installer+official+git](https://code.google.com/p/msysgit/downloads/list?q=full+installer+official+git) 当前最新版本是[Git-1.8.4-preview20130916.exe](https://code.google.com/p/msysgit/downloads/detail?name=Git-1.8.4-preview20130916.exe&can=2&q=full+installer+official+git)

2 安装过程next，无他

3 可能需要修改下系统环境变量，增加（安装在d盘）;D:\Program Files\Git\bin;D:\Program Files\Git\libexec\git-core;

4 查看是否安装成功：`git --version`

### 安装Bower ###

1 官方网站 [http://bower.io/](http://bower.io/)

2 用npm安装

    npm install -g bower

### 配置jekyll环境 ###
1 官方网址 http://jekyllrb.com/，官方给的安装文档地址 [http://www.madhur.co.in/blog/2011/09/01/runningjekyllwindows.html](http://www.madhur.co.in/blog/2011/09/01/runningjekyllwindows.html)

2 按步骤，安装ruby环境，下载目录 [http://rubyinstaller.org/downloads/](http://rubyinstaller.org/downloads/)，当前最新版本是 Ruby 2.0.0-p247和[DevKit-mingw64-32-4.7.2-20130224-1151-sfx.exe](http://cdn.rubyinstaller.org/archives/devkits/DevKit-mingw64-32-4.7.2-20130224-1151-sfx.exe)

， 按步骤安装ruby到C盘，解压devkit到D:\Program Files\DevKit，增加环境变量C:\Ruby200\bin

3 启动命令行，进入devkit解压目录（D:\Program Files\DevKit） 输入

    ruby dk.rb init
    ruby dk.rb install
    gem install jekyll

等待安装，安装完成后输入

    gem install jekyll

命令行会输出当前jekyll的版本

### 安装bootstrap ###
1 网址[https://github.com/twbs/bootstrap与http://getbootstrap.com/](https://github.com/twbs/bootstrap与http://getbootstrap.com/)

2 三种安装方式

[Download the latest release.](https://github.com/twbs/bootstrap/archive/v3.0.2.zip)
Clone the repo: `git clone https://github.com/twbs/bootstrap.git`.
Install with Bower: bower install bootstrap

更新nodejs的版本可以在命令行中输入： `npm update npm -g`