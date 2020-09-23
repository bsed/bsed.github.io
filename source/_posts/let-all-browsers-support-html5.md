---
title: 让所有的浏览器都支持html5
date: 2012-02-05 15:04:00
updated: 2015-08-09 15:08:42
tags: 
- nodejs
- express
- 文件上传
categories: 
- js

---
> IE8以下的IE版本对HTML5的新标签可以说都是不支持的，所以当我们的页面中使用了HTML5新标签的时候，IE8以下的浏览器便无法识别，导致我们的样式在这些浏览器下面失效。

还好，我们的开发者做了个html5shiv项目，目的就是为了让IE能识别HTML5 的元素。好吧，这些早该入土或者快入土的古老浏览器在强大的互联网新技术面，我想你们肯定想找个地缝钻进去了吧。

html5shiv项目地址：[http://code.google.com/p/html5shiv/](http://code.google.com/p/html5shiv/)


<!--more-->


# 使用方法:

 - 调用html5shiv.js

在元素里（你调用其他js或者css文件的前后都可以）使用IE注释条件调用：
当然，你如果不喜欢在GoogleCode上调用，你可以下载下来放到你的网站上。

 - reset HTML5常用新标签元素为块级元素

在你的样式文件中reset HTML5常用新标签元素为块级元素：

    article,
    aside,
    details,
    figcaption,
    figure,
    footer,
    header,
    hgroup,
    menu,
    nav,
    section
    {display:block;}

