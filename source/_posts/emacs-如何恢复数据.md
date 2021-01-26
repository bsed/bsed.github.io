---
title: "emacs 如何恢复数据"
categories: [ "Linux" ]
tags: [ "linux","emacs" ]
draft: false
slug: "emacs-how-to-recover-data"
date: "2012-09-20 21:12:00"
---

返回到上一次保存的状态

    M-x revert-buffer

打开emacs之后第一次保存时对原文件保留的备份文件


<!--more-->


filename~
可以`C-x C-f`打开该文件再将其另存为

恢复失去的变更
自动保存文件:#filename#

    M-x recover-file 

