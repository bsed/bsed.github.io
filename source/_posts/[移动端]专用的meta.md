---
title: "[移动端]专用的meta"
categories: [ "Css" ]
tags: [ "meta","mobile","html" ]
draft: false
slug: "mobile-dedicated-mextameta"
date: "2016-10-07 15:58:00"
---

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0, user-scalable=no">
```
`width`和`height`指令分别指定视区的逻辑宽度和高度。他们的值要么是以像素为单位的数字，要么是一个特殊的标记符号。width指令使用device-width标记可以指示**视区宽度应为设备的屏幕宽度**。类似地height指令使用`device-height`标记指示**视区高度为设备的屏幕高度**。 
`user-scalable`指令指定用户是否可以缩放视区，即缩放Web页面的视图。值为yes时允许用户进行缩放，值为no时不允许缩放 

`initial-scale`指令用于设置Web页面的初始缩放比例。默认的初始缩放比例值因智能手机浏览器的不同而有所差异。通常情况下设备会在浏览器中呈现出整个Web页面，设为1.0则将显示未经缩放的Web文档。 

`maximum-scale`和minimum-scale指令用于设置用户对Web页面缩放比例的限制。值的范围为`0.25`至`10.0`之间。与`initial-scale`相同，这些指令的值是应用于视区内容的缩放比例。 
所有智能手机浏览器都支持`ViewPort` <meta>标记的`width`和`user-scalabel`指令。但是Opera Mobile不使用user-scalable指令，而是主张用户应始终保留在移动浏览器中缩放Web页面的能力。 

## devicePixelRatio 

`window.devicePixelRatio`是设备上物理像素和设备独立像素(device-independent pixels (dips))的比例。公式表示就是：`window.devicePixelRatio = 物理像素 / dips` 

## fit-to-screen网站自适应屏幕 

如果content的值小于或等于`screen width` 网站自适应屏幕`fit-to-screen`将被关闭网站不会随着浏览器拉伸缩放;
如果content的值大于screen width,`fit-to-screen`将开启。 

*eg*:
```
<meta name="viewport" content="width=720">
```
## 苹果浏览器相关 

```html
<meta name="viewport" content="minimal-ui">
```

在iOS 7.1的Safari中为`meta`标签新增`minimal-ui`属性，让网页在加载时便可隐藏顶部的地址栏与底部的导航栏。 

```html
<meta name="apple-mobile-web-app-capable" content="yes">
```
是否启动webapp功能 设置为yes 网站就会在满屏模式full-screen mode删除默认的苹果工具栏和菜单栏 
```html
<meta name="apple-touch-fullscreen" content="yes"> 
```
开启对web app程序的支持 
```html
<meta name="format-detection" content="telephone=no"> 
<meta name="format-detection" content="email=no" /> 
```
iPhone会自动把你这个文字加链接样式、并且点击这个数字还会自动拨号！ 
telephone=no就忽略页面中的数字识别为电话号码 
telephone=yes就开启了把数字转化为拨号链接,在默认是情况下就是开启！ 
```html
<meta name="apple-mobile-web-app-status-bar-style" content="default" /> 
<meta name="apple-mobile-web-app-status-bar-style" content="black" /> 
<meta name=”"apple-mobile-web-app-status-bar-style" content=”black-translucent" /> 
```
控制状态栏显示样式:  `default`（白色）`black`（黑色） `black-translucent`（灰色半透明） 

## link标签apple-touch-icon 
如果 `apple-mobile-web-app-capable` 设置为 yes 了，那么在*iPhone*,*iPad*,*iTouch*的*safari*上可以使用添加到主屏按钮将网站添加到主屏幕上。 
而通过设置相应 `apple-touch-icon` 标签，则添加到主屏上的图标就会使用我们指定的图片。 
以下是针对ox不同设备，选择一个最优icon。默认iphone的大小为60px，ipad为76px，retina屏乘以2倍。
```html
<link rel="apple-touch-icon" href="touch-icon-iphone.png"> 
<link rel="apple-touch-icon" sizes="76x76" href="touch-icon-ipad.png"> 
<link rel="apple-touch-icon" sizes="120x120" href="touch-icon-iphone-retina.png"> 
<link rel="apple-touch-icon" sizes="152x152" href="touch-icon-ipad-retina.png"> 
```
ios7以前系统默认会对图标添加特效（圆角及高光），如果不希望系统添加特效，则可以用 `apple-touch-icon-precomposed.png` 代替 `apple-touch-icon.png` 

图标使用的优先级如下：
 

 - 如果没有跟相应设备推荐尺寸一致的图标，那个会优先使用比推荐尺寸大，但最接近推荐尺寸的图标。
 - 如果没有比推荐尺寸大的图标，会优先选择最接近推荐尺寸的图标。
 - 如些有多个图标符合推荐尺寸，会优先选择包含关键字precomposed的图标。
 - 如果未在区域指定用link标签指定图标，会自动搜索网站根目录下以 apple-touch-icon 为前缀的png图标。

**注**：`ios7`不再为`icon`添加特效，ios7以前则默认为icon添加特效，除非icon有关键字 `-precomposed.png` 为后缀。 

## IE浏览器 
```html
<meta http-equiv="X-UA-Compatible" content="IE=edge">  避免IE使用兼容模式 
<meta name="MobileOptimized" content="320">  微软为IE Mobile版设置的定义宽度标记
```
浏览器不会自动调整文件的大小,也就是说是固定大小,不会随着浏览器拉伸缩放。 
```
<meta name="HandheldFriendly" content="true" />：是否对手持设备友好，只有true或者false 
```

## UC浏览器 
```html
<meta name="screen-orientation" content="portrait">  强制竖屏 
<meta name="full-screen" content="yes">  强制全屏 
<meta name="browsermode" content="application">  应用模式 
```

## QQ浏览器 
```html
<meta name="x5-orientation" content="portrait">  强制竖屏 
<meta name="x5-fullscreen" content="true">  强制全屏 
<meta name="x5-page-mode" content="app">  应用模式 
```

## 360浏览器 
```html
<meta name="renderer" content="webkit">  开启急速模式 
```
