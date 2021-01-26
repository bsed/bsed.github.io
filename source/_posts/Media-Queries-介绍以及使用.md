---
title: "Media Queries 介绍以及使用"
categories: [ "Css" ]
tags: [ "css","media" ]
draft: false
slug: "the-media-setting-rate-at-different-resolution-font-size"
date: "2015-07-22 15:59:00"
---

## 通过@media设置在不同分辩率下字体的大小
```html
    <style>
    @media only screen and (min-width:320px){
      body{font-size:11.5px!important}.viewport{max-width:320px}
    }
    @media only screen and (min-width:360px){
      body{font-size:12.5px!important}.viewport{max-width:360px}
    }
    @media only screen and (min-width:480px){
      body{font-size:16px!important}.viewport{max-width:480px}
    }
    </style>
```

**新增**：

## Media 介紹以及使用

### 1. 在html引入样式的时候，将media引入其中

```html
<link rel="stylesheet" media="screen and (min-width: 400px) and (max-width: 700px)" href="test.css" />
```

### 2. 直接写在css样式里
```css
@media screen and (min-width: 420px) and (max-width: 860px) {
    a {
       color: red;}
    .entry {
       margin-top: 10px;
       font-size: 20px;}
}
```

### 3. 使用import导入
```css
/*在css里面使用 @import导入
一下解释为当屏幕宽度 在400px -700px 之间时， 将test.css 的导入到页面中 */
@import "test.css" screen and (min-width: 400px) and (max-width: 700px)
```

## Queries 介绍以及使用

Query 的用法只有三种：`not`, `and`, `only`。我们最常用的是`and`，这三个查询条件可以独立存在，也可以同时存在，只要用`,` 字符串隔开即可。

### and 使用方法：

#### 单个条件：
```CSS
/*如果屏幕分辨率为500px（含）以上，就是用下面的css 设定 */
@media screen and (min-width:500px) { 
  css code ...
}

/*如果屏幕视窗为竖屏， 就是用下面的css 设定
横屏： orientation:landscape */
@media screen and  (orientation: portrait) {
  css code ...
}
```

#### 同时符合两种条件的
```css
/*屏幕宽度在 400px ~ 700px 之间，就使用下面的css样式*/
@media screen and (min-width: 400px) and (max-width: 700px) {
  css code ...
}
```

#### 两种条件，符合一种即可
```css
/*如果屏幕是彩色的， 就是用下面的样式 */
@media screen and (color), projection and (color) {
  css code .
}
```

### only 使用方法：

我们知道，有的浏览器不支持 `Media Queries`，但支持 `Media Type`，我们可以使用only 来阻止这些浏览器。
```html
<link rel="stylesheet" type="text/css" href="test.css" media="only screen and (color)">
```
*解释*：

 - 支持 `Media Queries` & `Media Type` 的浏览器， 如果是彩色屏幕，就是用 `test.css`;
 - 不支持 `Media Queries`，但是支持 `Media Type` 的浏览器， 因为有 only 条件的限制，不会往下读彩色屏幕，
   所以不会使用 `test.css`;
 - 不支持 Media Queries & Media Type 的浏览器； 不管有没有 `only` 都不会使用 'test.css';


### not 使用方法：

not是用来排除某些设备的样式， 例如，你希望这个样式只在 A 设备上有作用， B 设备上没作用， 就可以使用 `not`。

例如：

```css
/*彩色屏幕不会使用下面css样式，彩色打印机会使用松下面css样式*/
@media not screen and (color), print and (color) {
  css code ...
}
```
我们可以使用`()` 来表现更加分明的逻辑关系 ：`@media (not (screen and (color))), print and (color)`

## Media 支持 type 和 features (值)

### Media Type

Media 常见的设备有： all, screen, print ，下面是比较全的。

 - all ：所有可。
 - braille ：盲人点字器。
 - embossed ：盲人印表机。
 - handheld ： 手机/PAD。
 - print ：打印机。
 - projection ：屏幕投影。
 - screen ：一般屏幕 (含iphone, ipad)。
 - speech ：朗读设备。
 - tty ：由文字长度决定大小的终端设备。
 - tv ：电视。

### Media Feature

 - width | min-width | max-width
   (寬度 | 最小寬度 | 最大寬度)
 - height | min-height | max-height
   (高度 | 最小高度 | 最大高度)
 - device-width | min-device-width | max-device-width
   (设备宽度 | 设备最小宽度 | 设备最大宽度)
 - device-height | min-device-height | max-device-height
   (设备高度 | 设备最小高度 | 设备最大高度)
 - orientation (value: portrait | landscape)
   (设备旋转方向 值:竖立 | 橫向)
 - aspect-ratio | min-aspect-ratio | max-aspect-ratio
   (屏幕显示比例 | 屏幕显示最小比例 | 屏幕显示最大比例)
 - device-aspect-ratio | min-device-aspect-ratio |
   max-device-aspect-ratio
   (设备显示比例 | 设备显示最小比例 | 设备显示最大比例)
 - color | min-color | max-color
   (彩色 | 最小顏色bit數 | 最大顏色bit數)
 - color:0 表示该设备为黑白(单色调)
 - color-index | min-color-index | max-color-index
 - monochrome | min-monochrome | max-monochrome
   (单色 | 最小單单色bit數 | 最大单色bit數)
 - monochrome :0 表示該设备不是黑白(单色)
 - resolution | min-resolution | max-resolution
   (解析度 | 最小解析度 | 最大解析度)
 - scan (value:progressive | interlace)
   (TV 使用)
 - grid