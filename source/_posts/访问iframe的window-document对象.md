---
title: "访问iframe的window/document对象"
categories: [ "JS" ]
tags: [ "iframe" ]
draft: false
slug: "windowdocument-object-to-access-iframe"
date: "2012-10-01 19:52:00"
---

iframe与宿主页面之间的通讯受 同源策略影响。

访问window对象：

    var iframeWindow = iframe.contentWindow;

访问document对象：

    // IE8以上及标准浏览器通过contentDocument引用，IE8之前的IE使用iframe.document
    var iframeDocument = iframe.contentDocument || iframe.document;