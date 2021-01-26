---
title: "在 Ubuntu 14.04 安裝 node.js"
categories: [ "JS" ]
tags: [ "nodejs","ubuntu","nvm" ]
draft: false
slug: "install-nodejs-1404-in-ubuntu"
date: "2012-11-19 21:12:00"
---

使用 nvm(Node Version Manage) 來安裝 node.js,
預先需要 curl, git, g++ :	

    $ sudo apt-get install git-core g++ curl

接著即可用以下指令安裝 :	

    $ git clone git://github.com/creationix/nvm.git ~/.nvm
    $ echo ". ~/.nvm/nvm.sh" >> ~/.bashrc


<!--more-->


重新開啟 terminal 或是打`source ~/.bashrc`或`. ~/.bashrc` 載入新的bashrc設定到目前bash環境中, 接著 :
	

    $ nvm install v0.8.9 # install current version, take some time
    $ nvm alias default v0.8.9 # set default node version
    $ node -v # verify

设置默认版本 `nvm alias default stable`

node.js 在 0.6.3 之後的版本開始內建 npm (Node Package Management),
可用 npm -v 作確認

*修改时间 2015年11月06日21:38:06*

我使用nvm来管理node的版本，可是当我使用sudo node时得的结果却是无法找到程序，which后发现node使用的是本地.nvm的目录下的node,而sudo时使用的是/usr/bin下的node，而且此时使用npm安装的包也是使用.nvm下的包，无法全局安装，那么请问如何使用nvm全局安装node！总的来说就是无法给现有的node的程序授予sudo执行的权限

nvm安装的都在本地，不能加 sudo的,sudo 回去/usr/bin 或/usr/local/bin下面去找的，遇到权限问题很蛋疼！
[https://cnodejs.org/topic/551f89ec9f883d8332bbf9af](https://cnodejs.org/topic/551f89ec9f883d8332bbf9af)
