---
title: 在Firefox,UC下显示动态icon图标
date: 2012-10-04 15:48:00
updated: 2015-10-04 15:52:21
tags: 
- iframe
- p3p
categories: 
- js

---
我们都知道我们可以写一段html来把浏览器的地址栏图标替换成自己网站的图标，但是你知道在不同你浏览器下显示不同icon图标的方法吗？尤其是在Firefox下还能显示动态的地址栏icon图标呢！下面icech就为大家讲解一下如何设置不同浏览器下不同的地址栏icon图标。

## 1、普通方式下的地址栏icon图标替换

### 方法1：制作一个ico图标，然后重命名为favicon.ico，将这个icon图标文件上传至网站的根目录下就可以了。

说明：这种方法适用于各种浏览器的地址栏icon图标和收藏夹图标。

### 方法2：在网页的head中加入以下代码：


<!--more-->


IE或者Firefox地址栏前换成自己的图标

    <link rel="icon" href="favicon.ico" type="image/x-icon">
    <link rel="Shortcut Icon" href="favicon.ico" type="image/x-icon">

可以在收藏夹中显示出你的图标

    <link rel="Bookmark" href="favicon.ico" type="image/x-icon">

说明：这种方式适用于IE浏览器。

## 2、在Firefox下显示动态的icon图标

首先制作出一个大小为16×16像素的gif图片，文件名没有要求。然后上传到icon目录下（任意目录都可以），然后在head中加入代码：

    <link rel="icon" href="icon/animated_favicon.gif" type="image/gif">

这样在Firefox浏览器下就能在地址栏中显示动态的icon图标了！但是这种方式不能再IE中显示动态效果。

所以同时使用两个代码，就可以同时在IE和Firefox浏览器的地址栏下显示不同的icon图标了。

代码如下：

    < link rel="icon" href="favicon.ico" type="image/x-icon"> 
    < link rel="Shortcut Icon" href="favicon.ico" type="image/x-icon">
    < link rel="icon" href="icon/animated_favicon.gif" type="image/gif">

**注意：**不论是icon格式还是gif格式的图标，大小都应该是16×16像素。代码“<link”中icech加了一个空格，使用的时候要注意去掉空格。

个人网站的手机版，想像其他大网站一样能在用户存储手机浏览器的主页书签时显示自己网站的识别标志。
这个已经添加上了

    <link rel="shortcut icon" href="http://www.xxx.com/favicon.ico" />
    <link rel="Bookmark"  href="http://www.xxx.com/favicon.ico"/>
    <link rel="apple-touch-icon" href="icon.png"/> 

。已经能够实现PC浏览器显示网站的识别小图标。手机浏览器的普通书签也可以实现显示小图标。唯独“主页书签”的识别图标还是显示网站首页的缩略图，已经在QQ手机浏览器和UC浏览器里测试了，都不能显示“主页书签”，在UC里叫“主页图标”。请大神解答。。。。