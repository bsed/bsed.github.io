---
title: "Linux命令tail"
categories: [ "Linux" ]
tags: [ "tail" ]
draft: false
slug: "linux-command-tail"
date: "2013-02-22 09:54:00"
---

linux `tail`命令用途是按照要求将指定的文件的最后部分输出到标准设备，一般是终端，通俗讲来，就是把某个档案文件的最后几行显示到终端上，如果该档案有更新，tail会自动刷新，确保你看到最新的档案内容。
工作中经常用tail命令查看PHP/Java错误日志,接口日志等.分享一下这个命令的用法!

## 一、tail命令语法


<!--more-->


tail [ -f ] [ -c Number | -n Number | -m Number | -b Number | -k Number ] [ File ]
*参数说明*：

 - `-f` 该参数用于监视File文件增长。
 - `-c Number` 从 Number 字节位置读取指定文件
 - `-n Number` 从 Number 行位置读取指定文件。
 - `-m Number` 从 Number
   多字节字符位置读取指定文件，比如你的文件如果包含中文字，如果指定-c参数，可能导致截断，但使用-m则会避免该问题。
 - `-b Number` 从 Number 表示的512字节块位置读取指定文件。
 - `-k Number` 从 Number 表示的1KB块位置读取指定文件。
 - `File` 指定操作的目标文件名

上述命令中，都涉及到`number`，如果不指定，默认显示10行。Number前面可使用正负号，表示该偏移从顶部还是从尾部开始计算。
tail可执行文件一般在`/usr/bin/`下面。
## 二、tail命令用法示例
### 1、tail -f filename
说明：监视filename文件的尾部内容（默认10行，相当于添加参数 -n 10），刷新显示在屏幕上。退出，按下CTRL+C。
### 2、tail -n 20 filename
说明：显示filename最后20行。
### 3、tail -n +20 filename
说明：显示filename前面20行。
### 4、tail -r -n 10 filename
说明：逆序显示filename最后10行。
## 补充：
跟tail功能类似的命令还有：
`cat` 从第一行开始显示档案内容。
`tac` 从最后一行开始显示档案内容。
`more` 分页显示档案内容。
`less` 与 more 类似，但支持向前翻页
`head` 只显示前面几行
`tail` 只显示后面几行
`n` 带行号显示档案内容
`od` 以二进制方式显示档案内容