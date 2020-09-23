---
title: 去掉awesome图片下的下划线
date: 2013-02-04 11:09:00
updated: 2015-08-04 11:11:31
tags: 
- python
- args
- kw
- function
categories: 
- python

---
    i[class^="icon-"]:before {
    	display: inline-block;
    	text-decoration: none;
        padding-right: 3px;
    }