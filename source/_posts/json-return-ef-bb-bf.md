---
title: json返回奇怪的EF BB BF
date: 2012-09-11 12:15:00
updated: 2014-12-25 12:16:51
tags: 
- android
- post
categories: 
- android

---
在ajax加载数据，使用json来传输数据，但是返回值中一直有EF BB BF这么一个字符串。解决方法如下：

> 把文件编码改成utf8-无bom编码
原因分析
微软在自己的UTF-8格式的文本文件之前加上了EF BB BF三个字节, windows上面的notepad等程序就是根据这三个字节来确定一个文本文件是ASCII的还是UTF-8的, 然而这个只是微软暗自作的标记, 其它平台上并没有对UTF-8文本文件做个这样的标记。