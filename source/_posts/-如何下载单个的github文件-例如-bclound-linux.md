---
title: " 如何下载单个的github文件 例如 bclound linux"
categories: [ "Linux" ]
tags: [ "linux","github","wget","bcloud" ]
draft: false
slug: "how-to-download-a-single-github-file"
date: "2012-11-21 08:08:00"
---

有时候我们仅仅需要下载单个的github文件。

1.打开需要下载文件的页面


2.点击右上角的raw键，这个是原生态的文件，同时地址栏的地址也是该文件的url。


<!--more-->


`https://github.com/LiuLang/bcloud-packages/raw/master/bcloud_3.8.1-1_all.deb`

3.下载该文件（命令行里）

打开你要下载文件的存储目录，然后敲击`wget -O xxxxx https://github.com/LiuLang/bcloud-packages/raw/master/bcloud_3.8.1-1_all.deb`就可以了。

其中-O表示重命名为xxxx，如果大家对wget命令感兴趣的话，可以谷歌下。


