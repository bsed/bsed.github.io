---
title: Div居中，Div里的内容居中
date: 2016-02-02 23:15:00
updated: 2016-02-02 23:22:30
tags: 
- js
- ecmascript7
categories: 
- js

---
好吧，记忆力越来越差了。

## 1.DIV居中：
主要的样式定义如下：

```css
body {text-align: center;}
#center { margin-right: auto; margin-left: auto; }
```
**说明：**

首先在父级元素定义text-align: center;这个的意思就是在父级元素内的内容居中；对于ie这样设定就已经可以了。但在mozilla中不能居中。解决办法就是在子元素定义时候设定时再加上“margin-right: auto;margin-left: auto; ”
需要说明的是，如果你想用这个方法使整个页面要居中，建议不要套在一个div里，你可以依次拆出多个div，只
要在每个拆出的div里定义margin-right: auto;margin-left: auto; 就可以了。

## 2.div中的内容垂直居中

```css
body{text-align: center;}
#center{ margin-right: auto;
margin-left: auto;
height:200px;
background:#f00;
width:400px;
vertical-align:middle;
line-height:200px;
}
```

**说明：**

`vertical-align:middle;`表示行内垂直居中，我们将行距增加到和整个div一样高
`line-height:200px;`然后插入文字，就垂直居中了。

## 3.div中的内容水平居中

```css
#center{
height:200px;
background:#f00;
width:400px;
text-align:center;}
```
text-align:center; 水平居中

本文来自：http://www.cnblogs.com/wj110reg/articles/850526.html
