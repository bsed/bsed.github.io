---
title: 解决fatal: The remote end hung up unexpectedly
date: 2016-10-12 23:59:00
updated: 2019-06-30 16:44:25
tags: 
- ubuntu
- tensorflow
- cuda
categories: 
- linux

---
解决方法：


<!--more-->


修改设置git config文件的postBuffer的大小。（设置为500MB）
```bash
$ git config --local http.postBuffer 524288000
```
注：--local选项指定这个设置只对当前仓库生效。