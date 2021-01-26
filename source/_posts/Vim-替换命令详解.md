---
title: "Vim 替换命令详解"
categories: [ "Vim" ]
tags: [ "vim","replace" ]
draft: false
slug: "replace-the-vim-command"
date: "2013-11-11 22:12:00"
---

日常编程中大量的使用查找和替换，各种编辑器和IDE都对这两个功能提供了很好的支持，但是相对与GUI界面的查找替换，Vim提供的方式就显得很反直觉，古怪的命令，永远也记不住的正则表达式…… 当然gvim也提供了一个GUI界面的查找替换，你可以在menu的Edit中找到，也可以用:promptrepl命令调出这个对话框，使用起来和其他编辑器没什么不同。但是这样的对话框是不符合Vim哲学的，毕竟学习反直觉的Vim，就是要在适应他的古怪后，得到效率的巨大提高，和装13时的满足感。所以还是扔掉对话框，来学习怎么用Vim的命令吧。

查找替换命令基本形式：

    :[range]s[ubstitute]/{pattern}/{string}/[flags] [count]

range

    `[％]` 表示整个文件
    `[20,60]` 表示从20行到60行
    `[.,$]` 表示从当前行到文件尾

option

    `[g]` 表示替换当前行中所有的匹配
    `[c]` 表示替换前需要确认
    `[i]` 表示忽略大小写

需要确认时提示输入(y/n/a/q/l/^E/^Y)，分别代表：

    y = Yes
    n = No
    a = all
    q = quit(停止替换)
    l = last(替换当前match, 停止剩下的替换操作)
    ^E = Ctrl-e(向上滚动屏幕)
    ^Y = Ctrl-y(向下滚动屏幕)

几个例子

    :%s/old-text/new-text/gc
    用new-text替换文件中所有的old-text, 每次替换前需要用户确认，
    :%s/\<old-word\>/new-word/g
    匹配整个单词并替换。当然使用正则表达式，你可以实现更多复杂的匹配
    :%s//new-text/g
    根据上一次的查找模式匹配并替换为new-text
    :%s/old-text/<c-r><c-w>/g
    将所有的old-text替换为当前光标下的word
    :%s/old-text/<c-r><c-a>/g
    将所有的old-text替换为当前光标下的WORD（两个空格间的所有字符）
    :%s/old-text/<c-r>a/g
    将所有的old-text替换为寄存器a当中的内容
    :%s//<c-r>//g
    这里<c-r>/会把上次的查找模式展开，用户可以在此之上进行修改，然后再进行替换
    :%s/old-text/<c-r>*/g
    将所有的old-text替换为系统剪切板当中的内容

## 少量替换的推荐方法

其实很多时候可能仅仅需要对几个变量做更改，范围不大，调用这么销魂的命令明显得不偿失。这种情况下，最快捷的方法还是将光标移动到要修改的变量名前（最好是通过search），cw键入新的变量名Esc，然后n跳转到下一个match，然后.重复替换操作……

## 多个文件或者全工程下的查找替换

Vim是没有工程的概念的，他的所有操作都是基于buffer(或者叫文件)的，要针对整个工程下的文件执行替换非常的不方便。如果执行大面积的重构等操作，我推荐过的EasyGrep正是不二之选。

当然如果你对Vim命令情有独钟，下面的命令一样解决问题

    :bufdo %s/pattern/replace/ge | update

针对所有的buffer(所有打开的文件)进行替换。还有更狠的：

    :arg *.cpp	 #当前路径下的所有cpp文件
    :argadd *.h	 #加上所有h头文件
    :argdo %s/pattern/replace/ge | update	#对arg中定义的所有文件执行替换操作