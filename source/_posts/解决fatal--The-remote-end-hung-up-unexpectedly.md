---
title: "解决fatal: The remote end hung up unexpectedly"
categories: [ "Linux" ]
tags: [ "git","fatal" ]
draft: false
slug: "solving-fatal-the-remote-end-hung-up-unexpectedly"
date: "2016-10-12 23:59:00"
---

解决方法：


<!--more-->


修改设置git config文件的postBuffer的大小。（设置为500MB）
```bash
$ git config --local http.postBuffer 524288000
```
注：--local选项指定这个设置只对当前仓库生效。