---
title: "[solved] mpv   mpv: relocation error: /usr/lib/x86_64-linux-gnu/libldb.so.1: "
categories: [ "Linux" ]
tags: [ "mpv" ]
draft: false
slug: "buspirone-10mg"
date: "2019-02-25 10:55:00"
---

最近 mpv 突然打不开，终端运行提示 libtalloc 版本有问题。

使用终端运行错误提示如下


<!--more-->


```bash
$ mpv  
mpv: relocation error: /usr/lib/x86_64-linux-gnu/libldb.so.1: symbol talloc_strdup_append_buffer version TALLOC_2.0.2 not defined in file libtalloc.so.2 with link time reference
```
解决：
```bash
sudo apt-get install --reinstall libtevent0 libtalloc2
```