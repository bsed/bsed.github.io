---
title: "优化Web App 的七条意见"
categories: [ "Css" ]
tags: [ "css" ]
draft: false
slug: "optimized-app-web-seven-views"
date: "2012-03-04 12:56:00"
---

# 1、Meta Viewport Tag

mete viewport是一个控制设备宽度和页面比例的一个HTML标签。使用这个标签，可以控制初始加载的页面缩放，控制页面缩放的最大比例，同时可以禁止缩放。

一种方法是在head里添加meta viewport标签

`<meta name="viewport" content="width=device-width; initial-scale=1.0; maximum-scale=1.0;">`
另一种方法是添加样式表

    @-viewport {  
        width: device-width;  
    }

如果你在制作一个响应式的网站，meta viewport是一个不可或缺的标签。

# 2、Media Queries 媒体查询

媒体查询可以方便的根据media属性中规定的触发条件调用指定的样式。media属性可以定义视窗宽度、横屏竖屏、屏幕像素密度等…

一种方法是在样式表标签添加media

    <link rel="stylesheet" media="screen and (orientation: portrait) and (min-width: 960px), projection" href="style.css" />

另一种方法是将代码写在样式表中，这个也是最常用的一种方法。

    @media screen and (max-width: 960px) {  
        /** Style Rules here **/  
    }

# 3、Modernizr

专为HTML5和CSS3开发的功能检测类库。过去做浏览器兼容都是用Hack，非常不规范。现在由于不同浏览器对新标准的支持不同，会出现不同的效果，如样式实现的圆角在谷歌支持，在IE却不支持。如何处理优雅降级，再不同浏览器下都能实现比较好的交互体验是挺重要的一门学问。

Modernizr检查后会在html标签添加样式的支持情况。

    <html class="js canvas canvastext no-geolocation rgba hsla   
    multiplebgs borderimage borderradius boxshadow opacity cssanimations csscolumns   
    cssgradients cssreflections csstransforms csstransforms3d csstransitions video   
    audio localstorage sessionstorage webworkers applicationcache fontface">

官网地址:[http://modernizr.com/](http://modernizr.com/)

# 4、TouchSwipe

[TouchSwipe](http://labs.rampinteractive.co.uk/touchSwipe/demos/)是一个专门针对移动设备实现触摸交互的库。它支持Swipe（滑动）、Pinch(按住)、Zoom(缩放)、Scroll(滚动)。
[demo](http://cool.techbrood.com/fiddle/5056)
下载地址:[https://github.com/mattbryson/TouchSwipe-Jquery-Plugin](https://github.com/mattbryson/TouchSwipe-Jquery-Plugin)
随着越来越多的移动设备摒弃按键标配触摸屏，将这个库结合到网站上，提高移动设备的用户体验。

# IOS Icons

打开iphone(or ipad)我们可以看到一堆应用图标，网站也可以通过浏览器中的添加到桌面将网站以应用图标的方式添加到桌面。

    <link rel="apple-touch-icon" href="touch-icon-iphone.png" />  
    <link rel="apple-touch-icon" sizes="72x72" href="touch-icon-ipad.png" />   
    <link rel="apple-touch-icon" sizes="114x114" href="touch-icon-iphone-retina.png" />

# Splash Screen 启动画面

打开网站会先进入启动画面，这个启动画面告诉我们应用正在加载中。同时一个好的启动画面可以让用户更好的理解产品。


# Windows 8 Pin Icon

window 8 和window rt同样有个将网添加到主屏磁贴。


    <meta name="msapplication-TileColor" content="#000"/>  
    <meta name="msapplication-TileImage" content="icon.png"/>

第一个meta是定义磁贴的颜色，第二个meta定义磁贴的图标。

原文地址：[http://www.hongkiat.com/blog/mobile-optimized-website/](http://www.hongkiat.com/blog/mobile-optimized-website/)