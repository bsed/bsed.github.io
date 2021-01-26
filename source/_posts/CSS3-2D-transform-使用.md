---
title: "CSS3 2D transform 使用"
categories: [ "Css" ]
tags: [ "css3" ]
draft: false
slug: "2d-transform-css3"
date: "2015-08-05 13:08:00"
---

#### 2D transform
通过 CSS3 2D转换, 我们能够对元素进行移动、缩放、转动、拉长或拉伸。

**浏览器支持**
`IE9,IE10,Chrome,FireFox`

**学习资料**
[W3Cschool Transform教程][3]
[Example1][4]
[Example2][5]

**Transform 常用变换函数**


<!--more-->


|函数|	描述|
|----| -----|
|matrix(n,n,n,n,n,n)|	定义 2D 转换，使用六个值的矩阵。|
|translate(x,y)|	定义 2D 转换，沿着 X 和 Y 轴移动元素。|
|translateX(n) | 定义 2D 转换，沿着 X 轴移动元素。|
|translateY(n) | 定义 2D 转换，沿着 Y 轴移动元素。|
|scale(x,y) | 定义 2D 缩放转换，改变元素的宽度和高度。|
|scaleX(n) | 定义 2D 缩放转换，改变元素的宽度。|
|scaleY(n) | 定义 2D 缩放转换，改变元素的高度。|
|rotate(angle) | 定义 2D 旋转，在参数中规定角度。|
|skew(x-angle,y-angle) | 定义 2D 倾斜转换，沿着 X 和 Y 轴。|
|skewX(angle)|定义 2D 倾斜转换，沿着 X 轴。|
|skewY(angle)|定义 2D 倾斜转换，沿着 Y 轴。|


**如何兼容IE8,7,6**
http://www.useragentman.com/IETransformsTranslator/

**扩展知识**
[2D矩阵变换原理][2]

    标准浏览器：matrix(1,0,0,1,0,0)
                matrix(a,b,c,d,e,f)
    
    IE8,7,6：   progid:DXImageTransform.Microsoft.Matrix(
                 M11=1, M12=0,M21=0,M22=1,SizingMethod='auto expand');
                M11 == a
                M12 == c
                M21 == b
                M22 == d

- 矩阵实现位移（disX和disY为偏移距离）
```math
x位移: e = e + disX 
y位移：f = f + disY
```
- 矩阵实现缩放（x和y为缩放倍数）
```math   
x轴缩放: a = x*a; c=x*c;  e=x*e
y轴缩放: b = y*b; d=y*d;  f=y*f
```
- 矩阵实现倾斜（xDeg和yDeg为扭曲角度）
```math
c = Math.tan(xDeg/180*Math.PI)
b = Math.tan(yDeg/180*Math.PI)
```
- 矩阵实现旋转（deg是旋转角度）
```math
a = Math.cos(deg/180*Math.PI);
b = Math.sin(deg/180*Math.PI);
c = -Math.sin(deg/180*Math.PI);
d = Math.cos(deg/180*Math.PI);
```
[矩阵函数的使用Example][0]

- 实战 在div元素中实现文字旋转居中
    - step1 写两个嵌套div(外层相对定位，内层绝对定位),外层的高是内层的宽,外层的宽是内层的高
    - step2 使内层div文字居中,可选,通过left,top调整内层div的位置 
    - step3 通过调整内层div的transform-origin, 以它为旋转中心，进行旋转

[0]:http://jsfiddle.net/hjzheng/xz4DG/2/
[1]:http://www.cnblogs.com/Clingingboy/archive/2010/10/17/1853559.html
[2]:http://www.useragentman.com/blog/2011/01/07/css3-matrix-transform-for-the-mathematically-challenged/
[3]:http://www.w3school.com.cn/css3/css3_2dtransform.asp
[4]:http://jsfiddle.net/hjzheng/BJU3P/
[5]:http://jsfiddle.net/hjzheng/UjuT5/2/
[6]:http://www.gbtags.com/gb/share/2388.htm

### Bluemix Case Study 
### Chrome Dev Tools mobile-emulation
https://developers.google.com/chrome-developer-tools/docs/mobile-emulation
### Google Material Design
http://design.1sters.com/