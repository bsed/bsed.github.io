---
title: 用CSS3 background-size属性控制背景URL图片大小
date: 2015-10-28 17:51:00
updated: 2016-01-31 17:53:46
tags: 
- plugins
- atom
categories: 
- css

---
background-size是CSS3提供的一个新特性，它可以让你随心所欲的控制背景图的尺寸大小。先简要的看看 [background-size的用法](http://demo.doyoe.com/css3/background-size/)。我们知道在CSS2中，背景图的大小在样式中是不可控的，比如要想使得背景图填充满某个区域，你要么重新做帐大点的图，要么就只能让它以平铺的方式来填充。

如今background-size让你既可以直接缩放背景图来填充满这个区域，也可以让你先给背景图设置大小，然后以设置好的尺寸去平铺满这个区域。

## 一起来认识认识background-size吧。

**background-size的值类型：**

background-size需要1个或2个值，这些值既可以是像素px，也可以是百分比%或auto，还可以是特定值cover, contain。


<!--more-->


**background-size的值定义：**

background-size可以设置2个值，1个为必填，1个为可选。
其中第1个值用于指定背景图的width，第2个值用于指定背景图的height，如果只给background-size设置1个值，则第2个值默认为auto（cover和contain特定值除外）。
```css
div{
   background-image:url(test.png);
   background-repeat:no-repeat;
   background-size:100px;
}
```

等价于：

```css
div{
   background-image:url(test.png);
   background-repeat:no-repeat;
   background-size:100px auto;
}
```
查看具体DEMO: [background-size的值定义](http://demo.doyoe.com/css3/background-size/background-size-value.htm)。当你使用firebug抓取到那个实例节点时，你会发现第二个值被自动加上了并且值为auto。当然，你也可以手动将第2个值设置为auto，然后与该DEMO的实例对比，它们的效果将是相同的。

**background-size的特定值：**

cover: 保持图像本身的宽高比例，将图片缩放到正好完全覆盖定义背景的区域；
contain: 保持图像本身的宽高比例，将图片缩放到宽度或高度正好适应定义背景的区域；
cover值：

```css
div{
   background-image:url(test.png);
   background-repeat:no-repeat;
   background-size:cover;
}
```
上例，背景图片将覆盖整个div区域。查看具体DEMO: [background-size:cover](http://demo.doyoe.com/css3/background-size/background-size-cover.htm)。

contain值：

```css
div{
   background-image:url(test.png);
   background-repeat:no-repeat;
   background-size:contain;
}
```

上例，背景图将缩放到宽度或高度的任意一边与div区域适应。查看具体DEMO: [background-size:contain](http://demo.doyoe.com/css3/background-size/background-size-cover.htm)。

[background-size简单的应用示例](http://demo.doyoe.com/css3/background-size/background-size-adapt.htm)
[设置背景图大小与平铺](http://demo.doyoe.com/css3/background-size/background-size-resize-repeat.htm)