---
title: Emacs技巧之宏编辑 
date: 2011-11-07 12:53:00
updated: 2015-11-14 13:02:58
tags: 
- linux
- hg
- mercurial
categories: 
- linux

---
## 宏编辑

定义: 所谓宏，就是一些命令组织在一起，作为一个单独命令完成一个特定任务.

## 宏操作

`C-x (` 或者 `F3` : 开始定义一个键盘操作宏.

`C-x )` 或者 `F4` : 结束定义一个键盘操作宏.

`C-x e` 或者 `F4` : 执行键盘宏

`C-u 37 C-x e` : 重复执行37次该宏

给宏起一个名字


<!--more-->


`C-x C-k n` 或者 `M-x kmacro-name-last-macro` `RET` 宏名字

插入指定名字的宏

`M-x insert-kbd-macro` `RET` 宏名字 `RET`

然后按 `C-x e` 或者 `F4` 或者 `C-u 232 C-x e` 或者 `C-u 232 F4` 来执行宏.
编辑最近一次的录制好的宏

`C-x C-k C-e` 或者 `kmacro-edit-macro`

编辑指定名字的宏

`C-x C-k e` 宏名字 `RET` 或者 `edit-kbd-macro`

