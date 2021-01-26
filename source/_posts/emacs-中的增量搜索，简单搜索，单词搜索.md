---
title: "emacs 中的增量搜索，简单搜索，单词搜索"
categories: [ "Linux" ]
tags: [ "linux","emacs" ]
draft: false
slug: "emacs-incremental-search-simple-search-word-search"
date: "2012-02-17 21:16:00"
---

增量搜索,逆向增量搜索，重复上一次搜索，逆向上一次搜索

    C-s
    C-r
    C-s C-s
    C-r C-r


<!--more-->


将下一个单词（一行）加入检索字符串，从kill-ring复制到检索字符串

    C-s C-w
    C-s C-y
    C-s M-y

上一个检索字符串，下一个检索字符串

    M-p
    M-n

简单搜索

    C-s Enter
    C-r Enter

单词搜索

    word-search-forward
    word-search-backward 

