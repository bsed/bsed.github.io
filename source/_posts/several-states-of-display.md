---
title: Display的几种状态值
date: 2010-11-01 16:34:00
updated: 2015-11-07 16:35:30
tags: 
- random
- sjs
categories: 
- js

---
Display的几种状态值
一般来说，Display有下列几种状态值

    div: {display: inline;}
    div: {display: block;}
    div: {display: inline-block;}
    div: {display: none;}
    div: {display: list-item;}
    div: {display: inherit;}

display: inline; —— 默认。此元素会被显示为内联元素，元素前后没有换行符。


<!--more-->


display: block; —— 此元素将显示为块级元素，此元素前后会带有换行符。

display: inline-block; —— 行内块元素。

display: none; —— 此元素不会被显示。

display: list-item; —— 此元素会作为列表显示。

display: inherit; —— 规定应该从父元素继承 display 属性的值。