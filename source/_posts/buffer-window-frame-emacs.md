---
title: emacs 中的buffer, window, frame基础
date: 2012-03-19 21:22:00
updated: 2015-07-20 21:23:31
tags: 
- linux
- emacs
categories: 
- linux

---
 显示buffer列表,切换或打开buffer

    C-x C-b
    C-x b

删除buffer，轮询删除不用的buffer

    C-x k
    kill-some-buffers


<!--more-->


垂直和水平分割当前Window为两个,去除一个窗口,只保留当前窗口

    C-x 1
    C-x 2
    C-x 3
    C-x 0

在另一个窗口中打开文件,只读打开一个文件,在另一个窗口中打开buffer

    C-x 4 f
    C-x 4 r
    C-x 4 b

滚动其他窗口
C-M-v

移动到其他窗口
C-X o

只保留当前frame,打开frame，关闭当前frame
C-x 5 1
C-x 5 2
C-x 5 0

在新frame中打开文件，只读方式打开文件，打开buffer
C-x 5 f
C-x 5 r
C-x 5 b

切换到另一个frame
C-x 5 o
