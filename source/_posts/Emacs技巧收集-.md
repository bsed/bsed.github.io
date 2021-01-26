---
title: "Emacs技巧收集 "
categories: [ "Linux" ]
tags: [ "emacs" ]
draft: false
slug: "emacs-collection"
date: "2012-11-05 11:07:00"
---

## Emacs中的按键说明

C-: 意思是按住 Ctrol 键
`M-‘: 意指 Meta 键 (在大多数键盘上指 Alt 键)
`DEL’: 意指退格键 (不是 删除(Delete) key)
`RET’: 意指回车键
`SPC’: 意指空格键
`ESC’: 意指Escape键
`TAB’: 意指Tab键

## 获取帮助

按下 `C-h` 后, 接着按以下

    `k` : 查看某键位的说明

    `f` : 查看某函数的功能

    `m` : 查看当前模式的键盘设置.

    `w` : 查看某个函数设定到哪个键上

    `a` : 查看包含某个字符串的所有函数

## `Undo` 和 `Redo`


<!--more-->


### undo

`C-/` 或者 `C-_` 或者 `C-x u`

### redo

`C-g C-/` 或者 `C-g C-_` 或者 `C-g C-x u`

redo 的思维就是 “undo undo”.

在光标处插入指定命令的输出结果

`C-u M-! 命令 RET`

## 打开 emacs 指南

`C-h t` 或者 `F1 t` 或者 `M-x help-with-tutorial`

如果你想看非英文的指南, 可以使用:

`M-x help-with-tutorial-spec-language RET` 然后选择你想看要看语种的指南.(有中文的哦)

## 替换
## 替换所有
## 完全匹配(非包含), 大小写敏感

`M-x replace-string RET` 要替换的字符串 RET 新的字符串 RET

### 包含匹配, 大小写不敏感

`M-x replace-regexp RET`要替换的字符串 RET 新的字符串 RET

## 查询并替换
完全匹配, 大小写敏感

`M-x query-replace RET` 要替换的字符串 RET 新的字符串 RET, 然后按 y 或者 n, 决定是否替换. q 或者 RET 为退出.

包含匹配, 大小写不敏感

`M-x query-replace-regexp RET` 要替换的字符串 RET 新的字符串 RET, 然后按 y 或者 n, 决定是否替换. q 或者 RET 为退出.

## 搜索
### 向后搜索
`C-r` 或者 `M-x isearch-backward` 搜索的字符串 RET

### 向前搜索

`C-s` 或者 `M-x isearch-forward RET` 搜索的字符串 RET

## 统计行数

    C-x l

## 插入一个 tab

    有时候, 想输入一个tab字符, 但是tab已经有它自己的作用, 特别是在org-mode, 或者是markdown-mode下, tab表示缩略标题及它的内容.这时如果想插入一个原始的tab字符,一般情况下就不适用了.这时可以按以下的做

    C-q TAB

`C-q` 表示下一个要输入的是原始(RAW)字符.
