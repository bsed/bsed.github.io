---
title: Emacs的Ido插件的基本用法
date: 2014-02-10 20:21:00
updated: 2015-11-27 20:22:12
tags: 
- java
- finialize
categories: 
- java

---
## IDO是啥？

ido是emacs下的一个用来切换缓冲区以及查找文件的神器，从emacs22开始就是emacs的一部分了

## 激活ido

在*.emacs*文件中添加下面的代码

    (require 'ido)
    (ido-mode t)

然后可以配置一下

    M-x customize-group RET ido RET

## 使用IDO
在buffer之间各种跳转，输入C-x b，然后


<!--more-->


输入buffer名的字符，RET后就可以浏览排在第一位的buffer了
可以使用C-s和C-r在list中间前后移动
[TAB]可以补全buffer名
使用C-f可以转换为发现文件(带ido-mode)，C-b返回到switch-to-buffer(不带ido-mode)
## 查找文件，输入C-x C-f
输入字符，按下回车就选中list中的第一个文件或者文件夹
C-s和C-r可以在整个list中来回选择
[TAB]用来补全在list中所有可能的结果
backspace进入上一级目录
//进入root目录
~/进入home目录
C-f回到没有ido-mode的查找文件功能，C-b回到带有ido-mode切换buffer功能
C-d打开当前的文件夹
C-j创建一个新的文件
## 最近访问的文件夹
输入M-p和M-n来选择历史记录中的文件夹
M-s查找匹配文件的输入
从历史记录中删除当前的文件夹