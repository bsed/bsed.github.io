---
title: "IE8中javascript trim方法失效"
categories: [ "JS" ]
tags: [ "trim" ]
draft: false
slug: "web-ie8-js-trim-no-use"
date: "2012-01-20 12:23:00"
---

这是一个很纠结的问题，之前一直没在意，后来偶然一次发现写的代码在IE8下竟然报错！并且报错的是trim方法，百思不得其解，后来发现原来我用的是javascript中原生的trim方法，但是IE8下的确没有这个方法。
解决方案如下：

    使用jquery的$.trim()方法搞定！