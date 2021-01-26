---
title: "IE下iframe存取cookie"
categories: [ "JS" ]
tags: [ "iframe","p3p" ]
draft: false
slug: "ie-iframe-access-cookie"
date: "2011-08-02 06:19:00"
---

IE6后引入了[P3P](http://www.w3.org/P3P/)。

解决方法：在回应的HTTP HEADER中输出P3P，比如在PHP中：

    header('P3P: CP=CAO PSA OUR');