---
title: emacs中fcitx不能用的解决办法 
date: 2012-10-21 21:09:00
updated: 2015-07-20 21:10:06
tags: 
- linux
- emacs
categories: 
- linux

---
 ~/.profile文件中LC_CTYPE变量改为
export LC_CTYPE="zh_CN.UTF-8" 