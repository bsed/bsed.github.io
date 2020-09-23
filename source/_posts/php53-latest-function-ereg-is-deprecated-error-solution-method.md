---
title: PHP Function ereg() is deprecated Error 解决方案
date: 2012-03-14 12:25:00
updated: 2014-12-25 12:28:39
tags: 
- cufon
categories: 
- js

---
在linux下折腾php开发，安装了最新的xampp 1.8,结果出现了如下问题： Function ereg() is deprecated Error.
**问题原因：**

> 在PHP5.3.0以后取消了对ereg函数的支持，所以会出现如下提示。

**解决方案：**

> 1.使用php 5.2版本（如果需要改动的很多，并且又不特别需要新版本的话，可以使用此方法） 2.修改程序源代码，把ereg替换成preg_match，ereg_replace替换成preg_replace.

**其他问题：**
注意：替换函数后，使用方法并不完全一致，请参照官方网站。