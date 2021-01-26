---
title: "在emacs中执行shell命令"
categories: [ "Linux" ]
tags: [ "linux","emacs" ]
draft: false
slug: "execute-shell-commands-in-emacs"
date: "2012-07-23 21:27:00"
---

 在Emacs中执行Shell Command

    M-!
    M-|
    C-u


<!--more-->


Shell模式命令

    M-x shell # 进入shell模式
    C-c C-c #break
    C-c C-z #stop
    C-d #eof
    C-c C-o #delete current output
    C-c C-r #光标移动到output的起始处
    C-c C-e #光标移动到output的结束处
    C-c C-p #光标移动到上一个output group
    C-c C-n #光标移动到下一个output group
    C-C C-U #删除该行输入的字符
    M-x rename-uniquely #更改为一个唯一的名
    M-x send-invisible 