---
title: "在 Ubuntu/Debian APT 仓库里查找文件"
categories: [ "Linux" ]
tags: [ "apt-file","uubntu" ]
draft: false
slug: "find-files-in-apt-ubuntudebian"
date: "2014-07-21 21:21:00"
---

`apt-file` 允许你通过Debian/Ubuntu APT Package 仓库搜索文件。

    apt-get install apt-file

初始化 apt-get 数据库

    apt-file update

最后通过下面的名字进行搜索

    apt-file search NAME


<!--more-->


