---
title: 整理了几个常见css属性
date: 2015-04-07 20:53:00
updated: 2015-04-22 21:19:30
tags: 
- Node-js
categories: 
- js

---
## 整理了几个CSS常用的属性。##

display属性:block/inline/none
display：block(作用是把行属性标签显示成块属性标签，可以设置宽高) ；
display：inline(作用是把块属性标签显示成行属性标签，这时块属性标签就不能设置宽高啦)；
display：none(作用是使所控制的标签不显示)
visibility：hidden(是设置元素的框的不可见，但是在布局中的位置是不变的)
<!--more-->
它和display：none的区别就在于后者不会占用那个位置，下一个元素会直接覆盖它，而前者是会占用那个布局，只是不显示内容，是 一片空白；并且下载的时候对于display：none就不会把不显示的元素给下载下来，而visibility：hidden则会把不显示的元素给下 载下来；

float（浮动，照样受文档流的限制）行标签float之后就可以设置它的宽高
float：none/left/right
none : 　对象不浮动
left : 　左浮动
right : 　右浮动

##clear（清除浮动）##
clear：both/none/left/right
none : 　允许两边都可以有浮动对象
both : 　不允许有浮动对象
left : 　不允许左边有浮动对象
right : 　不允许右边有浮动对象

## overflow（超出）##
overflow：visible/auto/hidden/scroll
visible : 　不剪切内容也不添加滚动条
auto : 　默认属性
hidden : 　隐藏超出内容
scroll : 　总是显示滚动条

## visibility(可视) ##

visibility : inherit/visible/hidden
inherit : 　继承上一个父对象的可见性
visible : 　对象可视
hidden : 　对象隐藏

几种图片格式的差别：

gif：不支持半透明
jpg：支持透明
png：部分支持透明，需要额外处理