---
title: "Bad value X-UA-Compatible for attribute http-equiv on element meta"
categories: [ "Css" ]
tags: [ "css","meta" ]
draft: false
slug: "value-xuacompatible-for-attribute-httpequiv-on-element-meta-bad"
date: "2013-10-05 13:58:00"
---

很多网站头部都有下面这行代码，

    <meta http-equiv="X-UA-Compatible" content="IE=8" />

这是专门针对IE浏览器的，解决不同版本IE的兼容问题，IE=8 表示“ 强制8以上的更先进的IE浏览器使用IE8的网页处理方式”。

这行代码由于是微软单方面提出的，不是HTML行业标准，所以无法通过 W3C检测
![yigrherb_X-UA-Compatible.jpg][1]
## 1.  在声明两端添加：  <!--[if IE]>     <![endif]--> 
变成了：

    <!--[if IE]><meta http-equiv="X-UA-Compatible" content="IE=8" /><![endif]-->


<!--more-->


这是HTML注释，W3C 检测会跳过这行，IE浏览器会识别。
然而，经笔者实验，很多情况下，IE并不能识别，漏掉了……

## 2. 不使用 <meta http-equiv=”X-UA-Compatible” content=”IE=8″ /> ，用.htaccess 替代。

打开网站根目录的 .htaccess 文件 (Apache 和 LiteSpeed  都有这个文件)，添加：

    Header set X-UA-Compatible "IE=8"

使用 http://www.webconfs.com/http-header-check.php  工具检测，会发现识别出了一行：

    X-UA-Compatible => IE=8

效果和在HTML里输出 `<meta http-equiv=" X-UA-Compatible" content="IE=8" />` 一样。 



![yigrherb_X-UA-CompatibleIE801.jpg][2]


  [1]: https://imgs.gnux.cn/usr/uploads/2016/01/1752584462.jpg
  [2]: https://imgs.gnux.cn/usr/uploads/2016/01/3096533521.jpg