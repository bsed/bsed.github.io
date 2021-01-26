---
title: "CSS去掉input标签和textarea标签聚焦的边框"
categories: [ "Css" ]
tags: [ "css" ]
draft: false
slug: "css-remove-input-tags-and-textarea-tags-focus-on-the-border"
date: "2015-01-01 10:04:00"
---

没怎么用过这标签，所以想着肯定是用CSS的边框属性给去掉聚焦后的边框的，就像下面的方法
```css
input:focus{border:0;}
```
但是做出来还是原汁原味的一样有边框，于是乎查了下资料发现其实是下面这样搞的
```css
input{outline:none} 
```
还有textarea标签的
```css
textarea{resize:none} 
```
