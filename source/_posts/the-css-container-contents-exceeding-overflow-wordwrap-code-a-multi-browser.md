---
title: CSS容器内容 溢出
date: 2013-03-18 15:30:00
updated: 2016-03-26 15:33:11
tags: 
- php
- vscode
categories: 
- php

---
完美支持IE，Firefox，Opera等浏览器。
```css
    .linebr { 
    clear: both; /* 清除左右浮动 */  
    width: 100px; /* 必须定义宽度 */  
    word-break: break-word; /* 文本行的任意字内断开 */  
    word-wrap: break-word; /* IE */  
    white-space: -moz-pre-wrap; /* Mozilla */ 
    white-space: -hp-pre-wrap; /* HP printers */ 
    white-space: -o-pre-wrap; /* Opera 7 */ 
    white-space: -pre-wrap; /* Opera 4-6 */ 
    white-space: pre; /* CSS2 */ 
    white-space: pre-wrap; /* CSS 2.1 */ 
    white-space: pre-line; /* CSS 3 (and 2.1 as well, actually) */ 
    } 
```


<!--more-->


<div class="linebr">CSS容器内容超出(溢出)自动换行,CSS容器内容超出(溢出)自动换行,CSS容器内容超出(溢出)自动换行</div