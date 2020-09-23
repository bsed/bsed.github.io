---
title: 打造emacs为IDE（二）
date: 2012-10-15 21:34:00
updated: 2015-07-20 21:37:52
tags: 
- jquery
- foreach
categories: 
- js

---
查看语法表

    C-h s

插入注释, 设置注释列, 插入下一行注释, 注释掉一段代码, 删除注释

    M-;
    C-x ;
    M-j
    M-x comment-region
    C-c C-c
    M-x kill-comment


----------


缩进，区域缩进, 移到本行第一个非空白字符处，删除缩进合并到上一行

    C-j variable(left-margin)
    C-M-\
    M-m
    M-^
    
    visit-tags-table
    M-.
    M-,
    M-x tags-search
    M-x tags-query-replace
    C-u M-x tags-query-replace
    M-x tags-apropos

