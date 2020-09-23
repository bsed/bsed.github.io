---
title: emacs 中的替换，正则表达式
date: 2012-06-11 21:17:00
updated: 2015-07-20 21:19:28
tags: 
- linux
- emacs
categories: 
- linux

---
简单替换

    replace-string

询问替换

    M-%


<!--more-->


重复需要用到minibuffer的命令

    C-x Esc Esc

递归编辑

    C-r
    C-M-c

关于检索替换是否区分大小写的变量

    case-fold-search
    case-replace

正则表达式
行首，行尾，词首，词尾，任意单个字符，零或多个字符，枚举字符中的一个，
空白符，不是空白符，数字，不是数字，字母数字下划线，不是字母数字下划线

    ^ $
    /< />
    . *
    []
    \s \S
    \d \D
    \w \W

正则表达式正反向增量搜索，询问替换

    C-M-s
    C-M-r

C-M-% 