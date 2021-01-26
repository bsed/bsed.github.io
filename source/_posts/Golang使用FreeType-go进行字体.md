---
title: "Golang使用FreeType-go进行字体"
categories: [ "Golang" ]
tags: [ "golang","freetype" ]
draft: false
slug: "golang-freetype-go"
date: "2012-12-01 12:21:00"
---

FreeType库（http://www.freetype.org/）是一个完全免费(开源)的、高质量的且可移植的字体引擎，它提供统一的接口来访问多种字体格式文件，包括TrueType, OpenType, Type1, CID, CFF, Windows FON/FNT, X11 PCF等。支持单色位图、反走样位图的渲染。


<!--more-->

freetype-go就是用go语言实现了FreeType驱动。它的项目地址： [https://code.google.com/p/freetype-go](https://code.google.com/p/freetype-go)
下面是使用它绘制的一个字体效果图：
![3998914994145271847.jpg][1]


  [1]: https://imgs.gnux.cn/usr/uploads/2014/11/3945569336.jpg