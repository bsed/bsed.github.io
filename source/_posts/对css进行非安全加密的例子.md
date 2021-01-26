---
title: "对css进行非安全加密的例子"
categories: [ "Css" ]
tags: [ "css","加密" ]
draft: false
slug: "examples-of-non-security-encryption-for-css"
date: "2015-08-07 13:22:00"
---

有些时候为了保护指定的样式表，可以采用混淆的方法扰乱查看者的视听。
比如下面这段：


<!--more-->


    @charset "utf-8";
    /*
    Theme Name: linstyle
    Version: 1.0
    Author: perfume
    */
    /*\0le\0u\0r\0y\0le@\0s\0.c\0s\0ss\0s\0s@@\0t u\0)r\0y\0le\0s u\0r\0y\0le\0s\0.c\0s\@0sy\0le\ u\0r\0y\0le\0s\0.c\0s\0s0s\0.c u\ u\0@r\0y\0l@e\0s\0.@c\0s\0s0r\0y\0le\0s\0.c\0s\0 t u\0r\0y@\0le\0u\0r\0y\0le@\0s\0.c\0s\0ss\0s\0s@@\0t u\0r\0y\0le\0s u\0r\0y\0le\0s\0.c\0s\@0sy\0le\ u\0r\0y\0le\0s\0.c\0s\0s0s\0.c u\ u\0*/@\00\0i\0\00m\0000\0p\0\0\0o\0r\0000t \000u\00r\0l\000("\0i\000ma\0\0\0\00ge\0\0s\0\0\0\0/\0\0l\0i\0\0\0\0\0\0n\0s\0\0t\0\0y\0\0le\0\0\0.c\0\0s\0s\0\0\0\0");\0\0m\0p\0o\0r\0t u\0r\0y\0le\0s\0.c\ u\0r\@0y\0le\0s\0.c(\0s\0s) u@\0r\0y\0le\0s\0.c\0s\0s u\0r(\0y\0le\0s\0.c\0@s\0s u\0r\0 u\0r\0y\0le\0s u\0r\0y\0le\0s\0.ct u\0@r\0y\0le\0\0s\0s\0.c\0s@\0t u\0r\0y\0le\0s u\0r\0y\0le\0s\0.c\0s\@0sy\0le\ u\0r\0y\0le\0s\0.c\(0s\0s0s\0.c u\ u\0@r\0y\0l@e\0s\0.@c\0s\0s0r\0y\0le\0s\0.c\0s\0 t u\0r\0y@\0le\0u\0r\0y\0le@\0s\0\0\0\0.c\0s\0ss\0s\0s@@\0t u)\0r\0y\0le\0s u\0r\\0\00y\0le\0s\0.c\0s\@0sy\0le\ u\0r\0y\0le\0s\\0\0\00.c\0s\0s0s\0.c u\ u\0@r\0y\0l@e\0s\0.@(c\0s\0s0r\0y\0le\0s\0.c\\0\0\00s\0 t u\0r\0y@\0le\0u\0r\0y\0le@\0s\0.c\0s\0ss\0s\0s@@\0t u\0)r\0y\0le\0s u\0r\0\0\0\0y\\0\0\00le\0s\0.c\0s\@0sy\0le\ u\0r\0y\0le\0s\0.c\0s\0s0s\0.c u\ u\0@r\0y\0l@e\0s\0.@c\0\0\0s\0s0r\0y\0le\0s\0.c\0s\0 t u\0r\0y@\0le\0u\0r\0y\0le@\0s\0.c\0s\0ss\0s\0s@@\0t u\0r\0y\0le\0s u\0r\\0\0\00y\0le\0s\0.c\0s\@0sy\0le\ u\0r\0y\0le\0s\0\0\0\0.c\0s\0s0s\0.c u\ u\0@r\0y\0l@e\0s\0.@c\0s\0s0r\0y\0le\0s\0.c\0s\0 t u\0r\0y@\0le\0u\\0\0\00r\0y\0le@\0s\0.c\0s\0ss\0s\\0\00s@@\0t u\0r\0y\0le\0s u\0r\0y\0le\0s\0.c\0s\@0sy\0le\ u\0r\0y\0le\0s\0.c\0s\0s0s\0.c \0\0\0u\ u\0@r\0y\0l@e\0s\0.@c\0s\0\0\0\0s0r\0y\0le\0s\0.c\0s\0 t u\0r\0y@\0le\0u\0r\0y\0le@\0s\0.c\0s\0ss\0s\0s@@\0t u\0r\0y\0le\0s u\0r\0y\0le\0s\0.c\0s\@0sy\0le\ u\0r\0y\0\0\0\0le\0s\0.c\0s\0s0s\0.c u\ u\0@r\0y\0l@e\0s\0.@c\0s\0s\0\0\00r\0y\0le\0s\0.c\0s\0 t u\0r\0y@\0le\0u\0r\0y\0le@\0s\0.c\0s\0ss\0s\0s@@\0t u\0r\0y\0le\0s u\0r\0y\0le\0s\0.c\0s\@0sy\0le\ u\0r\0y\0le\0s\0.c\0s\0\0\0\0s0s\0.c u\ u\0@r\0y\0l@e\0s\0.@c\0s\0s0r\0y\0le\0s\0.c\0s\0 t u\0r\0y@\0le\0u\0\0\0r\0y\0l\0\0\0e@\0s\0.c\0s\0ss\0s\0s@@\0t u\0r\0y\0le\0s u\0r\0y\0le\0s\0.c\0s\@0sy\0le\ u\0r\0y\0le\0s\0.c\0s\0s0s\0.c u\ u\0@r\0y\\0\0\00l@e\0s\0.@c\0s\0s0r\0y\0le\0s\0.c\0s\0 t u\0r\0y@\0le\0u\0r\0y\0le@\0s\0.c\0s\0ss\0s\0s@
    <style type="text/css">
    <!--
    html, body {scrollbar-face-color: #656cac;scrollbar-highlight-color: #a0a6d1;scrollbar-3dlight-color: #7b82c3;scrollbar-darkshadow-color: #3b4295;scrollbar-shadow-color: #7b82c3;scrollbar-arrow-color: #e8eaf9;scrollbar-track-color: #c2c5e6;}
    -->
    </style>

其实这个所谓的加密很简单:在CSS里`\0`、`\00`、`\000`是会被浏览器忽略的.
举个简单的例子:
`\0s\0t\0y\0l\0e\0.\0c\0s\0s`
去掉\0,\00什么的之后就是:style.css
