---
title: Typecho批量替换图片网址
date: 2012-02-09 13:41:00
updated: 2015-08-12 17:17:04
tags: 
- rails
categories: 
- rails

---
其实很简单，就是一句sql语句，但是很多童鞋对数据库不太熟悉，所以写一篇教程。
1.打开phpmyadmin，找到typecho的数据库
2.点上面的Sql选项


<!--more-->


3.输入下面的语句其中前一个网站为现在的地址，后一个为你想要替换成的网址

    UPDATE typecho_contents SET text = replace( text, 'www.xxx.com', 'www.xxx2.com' ) ;

<<<替换完成>>>