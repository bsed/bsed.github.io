---
title: css text-align属性
date: 2016-10-11 22:19:00
updated: 2016-10-11 22:20:00
tags: 
- javascript
- jstips
categories: 
- js

---
> text-align：start | end | left | right | center | justify |
> match-parent | justify-all

## justify：
内容两端对齐，但对于强制打断的行（被打断的这一行）及最后一行（包括仅有一行文本的情况，因为它既是第一行也是最后一行）不做处理。

## start：
内容对齐开始边界。


<!--more-->


## end：
内容对齐结束边界。

## match-parent：
这个值和 inherit 表现一致，只是该值继承的 start 或 end 关键字是针对父母的 <' direction '> 值并计算的，计算值可以是 left 和 right 。

## justify-all：
效果等同于 justify，但还会让最后一行也两端对齐。

