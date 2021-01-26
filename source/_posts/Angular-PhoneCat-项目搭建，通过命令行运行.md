---
title: "Angular-PhoneCat 项目搭建，通过命令行运行"
categories: [ "JS" ]
tags: [ "angularjs","scriptsweb-server.js" ]
draft: false
slug: "Angular-PhoneCat"
date: "2014-09-10 20:56:00"
---

PhoneCat项目的源代码托管在GitHub上，因此获取源代码之前需要安装Git (http://git-scm.com/download )。

安装Git后，可以通过git clone来下载源代码：

<!--more-->


1，获取angular-phonecat 项目源代码

输入命令：   `git clone --depth=20 https://github.com/angular/angular-phonecat.git`

–depth=20选项的意思为：仅下载最近20次的代码提交版本；这么做可以减少下载的文件大小，加快下载。

 

2，安装依赖包

输入命令：   `npm install`

PhoneCat是一个Web应用程序，因此最好在Web服务器中运行，以期获得最佳结果。

官方推荐安装Node.js( http://nodejs.org/download/ )。

 

3，Git 需要用https才能读到数据

默认情形下，用Git是通过http形式取数据，是无法取到数据的，必须用https方式来下载数据

解决方法：

输入命令:  `git config--global url."https://".insteadOf git://`

 

4，进入angular-phonecat目录，运行如下命令：

输入命令：   `git checkout -f step-0`

该命令将重置phonecat项目的工作目录，建议您在每一学习步骤运行此命令，将命令中的数字改成您学习步骤对应的数字，该命令将清除您在工作目录内做的任何更改。


5，启动样例工程

几乎所有的Angularjs学习教程，都会写到用这个命令来启动服务： `nodescripts/web-server.js`

但实际上PhoneCat项目已经放弃使用web-server了，git上取下来的的项目里没有 scripts/web-server.js 文件了。

我们可以用下面的方式来启动工程：

输入命令：   `npm start`

通过 http://localhost:8000/app/index.html访问

在线学习教程：  http://www.angularjs.cn/tag/AngularJS