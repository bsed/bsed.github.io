---
title: [转]移动web页面支持弹性滚动的3个方案
date: 2014-10-12 21:36:00
updated: 2015-11-07 21:51:43
tags: 
- js
- DeviceMotionEvent
categories: 
- js

---
`position:fixed` 和 `overflow:auto` 进行简单的布局实现我们需要的效果，而在手机端遇到的问题如下：

 - ios4 和 android2.2 以下不支持 position:fixed
 - ios 不支持 overflow:auto
 - ios4 和 android2.3 以下以及不支持 overflow-scrolling

最严重的结果是：滚动区域内容无法拖动
对于 ios4 和 android2.2 以下不支持 `position:fixed` 的问题，有2种布局方法可以替代。

## 布局一： 定义页面整体高度为100%

然后使用 position:absolute 布局可解决


<!--more-->


    <!--absolute布局 [[ -->
    <body>
    <div class="header">header</div>
    <div class="main">     弹性滚动区域    </div>
    <div class="footer">footer</div></body>
    <!--absolute布局 ]] -->
    
    html,body{height:100%;}
    .header,.footer{height:40px;line-height:40px;background-color:#D8D8D8;text-align:center;}
    .header{position: absolute;top:0;left:0;width:100%;}
    .footer{position: absolute;bottom:0;left:0;width:100%;}
    .main{position:absolute;z-index:1;top:40px;left:0;bottom:40px;width:100%;}

## 布局二： 定义页面整体高度为100%，

然后使用 display:flex 布局可解决

    <!-- flex布局 [[ -->
    <body><div class="wrap">
    <div class="header">header</div>
    <div class="main">       弹性滚动区域      </div>
    <div class="footer">footer</div></div>
    </body>
    <!-- flex布局 ]] -->
    
    html,body{height:100%;}
    .wrap{display:-webkit-box;display:-webkit-flex;display:-ms-flexbox;display:flex;
      -webkit-box-orient:vertical;-webkit-flex-direction:column;-ms-flex-direction:column;
      flex-direction:column;width:100%;height:100%;}
    .header,.footer{height:40px;line-height:40px;background-color:#D8D8D8;text-align:center;}
    .main{-webkit-box-flex:1;-webkit-flex:1;-ms-flex:1;flex:1;width:100%;}

那么剩下的主要问题是子容器高度超出父容器高度，子容器内容如何弹性滚动。

对于如何使用弹性滚动，这里并没有最好的方案，具体看产品的用户群、产品的定位等，简单介绍下：

## 方案一： overflow:auto和-webkit-overflow-scrolling: touch

overflow:auto 写法在 winphone8 和 android4+ 上有用。ios5+ 版本增加了一个新的属性：`overflow-scrolling` 这个属性可以激活平滑滚动，效果不错。

    .css{
      overflow:auto; /* winphone8和android4+ */
      -webkit-overflow-scrolling: touch;  /* ios5+ */
    }

适合场景：如果产品的用户群大多为 ios5+、android4+ 用户，建议采用 overflow-scrolling 做差异化体验，毕竟 iscroll4.js 在 android 机器下体验不顺畅，另外还加载了 10K 多的 js 代码。

## 方案二： iscroll4.js和overflow:auto

曾写过【使用iScroll.js解决ios4下不支持｀position:fixed｀的问题】，使用 iscroll4.js 基本上解决了页面弹性滚动的问题，总结下 iscroll4.js 的体验：

 1. 在 ios 系统上的表现十分良好，滚动顺畅
 2. 在部分 android 系统上性能较差，特别是滚动区域内容多时，滚动页面会出现卡顿
 3. ios 和 android 系统下有不少 bug，如表单获焦弹出软键盘后页面高度没有重新计算、出现闪屏等(这里不做讨论)
 4. winphone 不支持

那么这里的处理方案是，页面初始化时判断是 weibit 浏览器则启用 iscroll4.js

    <div class="wap ie-ova" id="">
    <!-- webkit 用户设置 ID 为 iscroll，可启用iscroll --> ...
    </div>

winphone8 手机使用如下 hack

    @media screen and (-ms-high-contrast: active), (-ms-high-contrast: none) {
    .ie-ova{overflow:auto;} /* winphone8 */
    }

适合场景：如果产品的用户群有 ios 和大部分 android2+ 用户，而在 android 中的页面数据比较简单(通常弹性滚动数据只有文字)，那么使用 iscroll4.js 可保证 android2+ 的机器展现正常也不卡顿，让ios用户滚动更顺畅。

##　方案三： iscroll4.js和overflow:auto和android2x.css

如果产品的用户群有ios和大部分android2+用户，而在 android 中页面数据比较复杂(通常弹性滚动数据有大量图片)，那么可针对 android2+ 的机器做静态定位展现(position:static)，页面不具备滚动效果，而对于 ios 用户仍然使用 iscroll4.js。

    <link rel="stylesheet" href="android2x.css">
    <!-- android2+ 用户多引用的css文件 -->

    .css{overflow:auto;/* winphone8和android4+ */}
    <div class="wap" id=""><!-- ios 用户设置 ID 为 iscroll，可启用iscroll --> ...</div>

原文地址：[http://www.th7.cn/web/html-css/201402/18119.shtml](http://www.th7.cn/web/html-css/201402/18119.shtml)
