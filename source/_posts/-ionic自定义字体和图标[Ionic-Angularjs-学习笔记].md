---
title: " ionic自定义字体和图标[Ionic Angularjs 学习笔记]"
categories: [ "JS" ]
tags: [ "angularjs","ionic" ]
draft: false
slug: "ionic-custom-fonts-and-icons"
date: "2015-09-04 21:06:00"
---

## ionic自定义图标和字体

Ionic 框架中内置了很多图标，我们只需要引用 ionic icons 中的样式即可。但是有时我们需要自己的图标，因此我们需要定义自己的图标。

**原理**

Ionic 中内置的所有图标，都是通过字体文件以css 样式的方式引入修改起来太麻烦

因此我们想要自己的图标，可以参照这种方式，定义自己的图标 ，自定义图标实际上就成了自定义字体，归纳起来可分为三步
1. 这里我们需要自己准备 svg格式的矢量图 
2. 生成字体文件
3. 在CSS中引用字体文件


<!--more-->


### 自定义图标操作步骤
1. 将要生成的图标 做成svg格式
2. 到 https://icomoon.io/app/#/select 网站生成图标
3. 解压下载后的压缩包 可以得到以下的文件夹 其中包括demo ，style.css 及fonts 文件中的字体文件就是我们要的
 使用时，一起拷贝到我们的工程中即可

