---
title: "emacs 中的Buffer相关命令"
categories: [ "Linux" ]
tags: [ "linux","emacs" ]
draft: false
slug: "buffer-related-commands-in-emacs"
date: "2012-08-21 21:23:00"
---

保存所有与文件关联的buffer

    C-x s

更改buffer名

    rename-buffer


<!--more-->


在只读与可写状态间切换

    C-x C-q

在*Buffer List*中的操作 上下,删,执行,撤销,保存,只读

    n p d x u Del s %

在*Buffer List*中的操作 只保留选中的window,替换window中的buffer等

    1 f o C-o m v

退出buffer list

    q

