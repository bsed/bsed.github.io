---
title: "CSS实现兼容浏览器自动换行"
categories: [ "Css" ]
tags: [ "css" ]
draft: false
slug: "css-compatible-browser-wrap"
date: "2012-08-11 10:33:00"
---

# CSS实现兼容浏览器自动换行
CMS类必备样式
```
.content {
    white-space: pre-wrap;
    *white-space: pre;
    *word-wrap: break-word;
}
```