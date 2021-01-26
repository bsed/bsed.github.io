---
title: "emacs中fcitx不能用的解决办法 "
categories: [ "Linux" ]
tags: [ "linux","emacs" ]
draft: false
slug: "solutions-for-fcitx-in-emacs"
date: "2012-10-21 21:09:00"
---

 ~/.profile文件中LC_CTYPE变量改为
export LC_CTYPE="zh_CN.UTF-8" 