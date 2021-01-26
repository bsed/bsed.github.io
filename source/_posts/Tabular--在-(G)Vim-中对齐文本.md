---
title: "Tabular: 在 (G)Vim 中对齐文本"
categories: [ "Java" ]
tags: [ "vim","Tabular" ]
draft: false
slug: "tabular-g-in-the-alignment-of-the-text-in-vim"
date: "2010-05-16 20:26:00"
---

Vim 插件 Tabular 允许你在 Vim 中按等号、冒号、表格等来对齐文本，对于经常写代码的朋友来说，有 Tabular 将会非常方便。
![tabular.png][1]
如上图所示，假如我想让其中的两行按等号对齐，则将光标定位到有等号的那行，执行` :Tab /= `即可。


<!--more-->


又如，若想将下面的

|1|2|
|one|two|
变成

| 1   | 2   |
| one | two |
可执行` :Tab /|`。


  [1]: https://imgs.gnux.cn/usr/uploads/2015/05/3165461514.png