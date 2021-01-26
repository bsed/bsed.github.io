---
title: "让IE浏览器支持CSS3圆角属性的方法"
categories: [ "Css" ]
tags: [ "css3" ]
draft: false
slug: "ways-to-make-ie-browsers-support-css3-fillet-properties"
date: "2013-01-02 11:20:00"
---

如果要想在IE浏览器中实现圆角的效果，我们一般都会采用圆角图片的方式。用图片的话，基本就跟浏览器没有多大关系了，因为任何浏览器都支持这种方式。今天我们主要是讲解如果用CSS3样式表来实现圆角效果，值得注意的是，IE6/7/8这三个IE浏览器版本都不支持CSS3的解析，只有还不太主流的IE9支持CSS3和HTML5的标准。让IE支持CSS3的解析方法有很多种，（[让IE浏览器支持HTML5标准的方法](http://www.iefans.net/ieliulanqi-zhichi-html5-fangfa/)）下面介绍一种实用的让IE支持CSS3解析的方法——*IE利用VML矢量可标记语言作为画笔绘出圆角*：


<!--more-->


1、下载一个压缩包，里面有一个微软的脚本文件（11KB）和一个用来测试服务器是否有正确的Content-Type的HTML文件：ie-css3.htc；.htc文件是IE内核支持Web行为后用来描述此类行为的脚本文件。它们定义了一套方法和属性，程序员几乎可以把这些方法和属性应用到HTML页面上的任何元素上去。Web 行为是非常伟大的因为它们允许程序员把自定义的功能“连接”到现有的元素和控件，而不是必须让用户下载二进制文件（例如ActiveX 控件）来完成这个功能。

文件下载：[iecss3][1]

解压后，打开test.html，如果显示效果是圆角，则可以继续。

演示代码：

    .main{
    border: 2px solid #C0C0C0;
    -moz-border-radius: 10px;
    -webkit-border-radius: 10px;
    border-radius: 10px;
    position:relative;
    z-index:2;
    behavior: url(此处为ie-css3.htc文件的绝对路径);
    }

Webkit内核的浏览器支持“-webkit-border-radius: 10px;”属性（10px是圆角半径），可以直接解析出圆角；Firefox浏览器支持“-moz-border-radius: 10px;”属性，也是可以直接解析出圆角；IE系浏览器则需要加上“border-radius: 15px;”的属性。

注意：

1、behavior的url里一定要填写ie-css3.htc的绝对路径，因为 IE浏览器找该文件是相对当前html文件路径来找的，所以对于Wordpress等动态程序生成的页面一定要填写绝对路径。

2、一定要有定位属性：position:relative;

3、因为在IE浏览器下这些CSS3效果的实现是要借助于VML，由VML绘制圆角或是投影效果，所以还需要一个z-index属性。z-index属性最好设置得比较大，如2。

4、如果在IE浏览器下某些模块无法用此渲染，可以试着绝对定位相应的层，即加上“ width: 400px; height:400px;”属性。

5、radius属性的10px是圆角半径，还可以给两个值如“border-radius: 10px 5px;”，这样则左上角与右下角半径为10px，右上角与左下角半径为5px。也可以赋4个值，为“上  右  下  左”。


  [1]: https://imgs.gnux.cn/usr/uploads/2015/03/4251322626.rar