---
title: "Axel - Linux多线程下载工具"
categories: [ "Linux" ]
tags: [ "linux","axel" ]
draft: false
slug: "linux-axel-multithreaded-download-tool"
date: "2016-11-07 21:27:00"
---

一个非常简单的多线程下载工具，解决单线程下载速度慢的问题，首先是安装：
```bash
apt-get install axel #debian/ubuntu安装方法
yum install axel #centos安装方法
```
使用方法，就几个参数，非常简单：
```bash
axel -n 10 http://......
```
另外还有一些参数，不太常用，请自行参考：


<!--more-->


```bash
-o /tmp #指定下载目录

-q #静默模式

-s #限制最大速度
```