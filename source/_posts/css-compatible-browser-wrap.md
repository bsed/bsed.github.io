---
title: CSS实现兼容浏览器自动换行
date: 2012-08-11 10:33:00
updated: 2015-08-11 10:34:22
tags: 
- css
- postcss
- grace
categories: 
- css

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