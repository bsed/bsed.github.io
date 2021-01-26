---
title: "normalize.css 其中一段代码的作用？"
categories: [ "Css" ]
tags: [ "css","normalize" ]
draft: false
slug: "normalizecss-the-role-of-a-section-of-code"
date: "2015-08-06 15:17:00"
---

`normalize.css`中的这段代码作用是什么？

    article,aside,details,figcaption,figure,footer,header,hgroup,main,menu,nav,
    section,summary {
      display: block;
    }


<!--more-->


这是html的结构化标签，但IE8以更早版本的不支持这些标签，所以要设置display：block，(也就是说这些标签的默认样式只是display：block),兼容ie8下浏览器的，ie下浏览器不认识这些标签。
原文：[http://segmentfault.com/q/1010000003014121](http://segmentfault.com/q/1010000003014121)