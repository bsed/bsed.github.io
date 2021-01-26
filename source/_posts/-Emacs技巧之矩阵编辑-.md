---
title: " Emacs技巧之矩阵编辑 "
categories: [ "Linux" ]
tags: [ "linux","emacs" ]
draft: false
slug: "matrix-editing-of-emacs-techniques"
date: "2013-11-24 13:04:00"
---

在开始进行矩阵编辑之前, 首先要标记所要编辑的矩阵.矩阵的选择, 是指两个对角的点所在的范围里.

在矩阵编辑中, 所有的指令, 都是以 C-x r 开头的.

## 矩阵的选择

标记对角的起点. [C-@]
标记对角的终点. [移动光标到对角的另一点的位置即可]

## 剪切矩阵的内容

`C-x r k`

或者


<!--more-->


`M-x kill-rectangle`

以某字符串替代矩阵里的每一行的内容

**注意**,首先要选择好所要编辑的矩阵内容

`C-x r t string RET`

或者

`M-x string-rectangle`

粘贴已经复制好或剪切好的矩阵内容

`C-x r y`

或者

`M-x yank-rectangle`

为矩阵内的每一行,添加行数数字.

行数, 是以所选择的内容为起始数字, 第一行为1.

C-x r N`

或者

`M-x rectangle-number-lines`

更多内容, 请参考

[Emacs Rectangles](http://www.gnu.org/software/emacs/manual/html_node/emacs/Rectangles.html)

[Emacs Rocks!关于矩阵操作的视频](http://emacsrocks.com/e01.html)
