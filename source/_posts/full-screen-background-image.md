---
title: 背景图片充满整个屏幕
date: 2014-02-11 20:08:00
updated: 2017-02-05 20:10:02
tags: 
- redis
- spring
categories: 
- java

---
body标签设置背景图，`<body class="bg">`

其实，块级容器的宽高是动态的，那么背景图将自动伸缩，充满整个容器。

**CSS bg 的样式如下：**
```css
.bg{
   /* 加载背景图 */
  background-image: url(../images/bg.jpg);
  /* 背景图垂直、水平均居中 */
  background-position: center center;
  /* 背景图不平铺 */
  background-repeat: no-repeat;
  /* 当内容高度大于图片高度时，背景图像的位置相对于viewport固定 */
  background-attachment: fixed;
  /* 让背景图基于容器大小伸缩 */
  background-size: cover;
  /* 设置背景颜色，背景图加载过程中会显示背景色 */
  background-color: #464646;
}
```