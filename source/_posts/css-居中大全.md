---
title: "css 居中大全"
categories: [ "Css" ]
tags: [ "css","居中" ]
draft: false
slug: "css-center"
date: "2013-09-26 21:02:00"
---

`<center>`

不建议用了。
`text-align:center`
在父容器里水平居中 inline 文字，或 inline 元素

`vertical-align:middle`
垂直居中 inline 文字，inline 元素，配合 display:table ，display:table-cell，有奇效。

`line-height`
与 height 联手，垂直居中文字
margin:auto


<!--more-->


**示例：**

    <style>
      #ex2_container { width:200px; background-color:yellow; }
      #ex2_content { margin:0px auto; background-color:gray; color:white; display:table; }
    </style>
    <div id="ex2_container"><div id="ex2_content">Hello,Gnux.CN</div></div>
hacks, hacks（小技巧）

有许多 [hacks](http://blog.themeforest.net/tutorials/vertical-centering-with-css/) ，负 margin，[影子元素](http://css-tricks.com/centering-in-the-unknown/) ::before 等。如果你的内容不是固定大小的话，它们大部分是很脆弱的。
translate(-50%,-50%)

用 position 加 translate
translate(-50%,-50%) 比较奇特，百分比计算不是以父元素为基准，而是以自己为基准。

参考文章：[居中百分比宽高的元素](http://jinlong.github.io/blog/2013/07/08/centering-percentage-widthheight-elements/)

示例：

    <style>
      #ex3_container { width:200px; height:200px; background-color:yellow; position:relative; }
      #ex3_content { left:50%; top:50%; transform:translate(-50%,-50%); -webkit-transform:translate(-50%,-50%); background-color:gray; color:white; position:absolute; }
    </style>
    <div id="ex3_container"><div id="ex3_content">Hello World</div></div>

这个技巧相当嚣张，同样适用于没固定大小的内容，min-width，max-height，overflow:scroll 等。

## 绝对定位居中

父容器元素：`position: relative`

    .Absolute-Center {
      width: 50%;
      height: 50%;
      overflow: auto;
      margin: auto;
      position: absolute;
      top: 0; left: 0; bottom: 0; right: 0;
    }

注意：高度必须定义，建议加 overflow: auto，防止内容溢出。
## 视口居中

内容元素：position: fixed，z-index: 999，记住父容器元素 position: relative

    .Absolute-Center.is-Fixed {
      width: 50%;
      height: 50%;
      overflow: auto;
      margin: auto;
      position: fixed;
      top: 0; left: 0; bottom: 0; right: 0;
      z-index: 999;
    }

[模态窗口实例](http://codepen.io/shshaw/full/gEiDt#Fixed)

## 响应式

百分比宽高，最大、最小宽度均可以，加 padding 也可以

    .Absolute-Center.is-Responsive {
      width: 60%; 
      height: 60%;
      min-width: 400px;
      max-width: 500px;
      padding: 40px;
      overflow: auto;
      margin: auto;
      position: absolute;
      top: 0; left: 0; bottom: 0; right: 0;
    }

## 偏移

只要 margin: auto; 在，内容块将垂直居中，top, left, bottom, right 可以设置偏移。

    .Absolute-Center.is-Right {
      width: 50%;
      height: 50%;
      margin: auto;
      overflow: auto;
      position: absolute;
      top: 0; left: auto; bottom: 0; right: 20px;
      text-align: right;
    }

## 溢出

居中内容比父容器高时，防止溢出，加 overflow: auto （没有任何 padding 时，也可以加 max-height: 100%;）。

    .Absolute-Center.is-Overflow {
      width: 50%;
      height: 300px;
      max-height: 100%;
      margin: auto;
      overflow: auto;
      position: absolute;
      top: 0; left: 0; bottom: 0; right: 0;
    }

## 调整尺寸

resize 属性可以让尺寸可调。
设置 min- /max- 限制尺寸，确定加了 overflow: auto 。

    .Absolute-Center.is-Resizable {
      min-width: 20%;
      max-width: 80%;
      min-height: 20%;
      max-height: 80%;
      resize: both;
      overflow: auto;
      margin: auto;
      position: absolute;
      top: 0; left: 0; bottom: 0; right: 0;
    }

## 图像

图像同样适用，设置 height: auto; 

    .Absolute-Center.is-Image {
      width: 50%;
      height: auto;
      margin: auto;
      position: absolute;
      top: 0; left: 0; bottom: 0; right: 0;
    }

## 可变高度

高度必须定义，但可以是百分比或 max-height。不想定义高度的话，用 display: table （需要考虑 Table-Cell 兼容性）。

    .Absolute-Center.is-Variable {
      display: table;
      width: 50%;
      overflow: auto;
      margin: auto;
      position: absolute;
      top: 0; left: 0; bottom: 0; right: 0;
    }

## 负 margin

确切知道宽高，负 margin 是宽和高的一半。

    .is-Negative {
            width: 300px;
            height: 200px;
            padding: 20px;
            position: absolute;
            top: 50%; left: 50%;
            margin-left: -170px; /* (width + padding)/2 */
            margin-top: -120px; /* (height + padding)/2 */
    }

## Table-Cell
参考文章：[Flexible height vertical centering with CSS, beyond IE7](http://www.456bereastreet.com/archive/201103/flexible_height_vertical_centering_with_css_beyond_ie7/)

结构：

    <div class="Pos-Container is-Table">
      <div class="Table-Cell">
        <div class="Center-Block">
        <!-- CONTENT -->
        </div>
      </div>
    </div>

样式：

    .Pos-Container.is-Table { display: table; }
    .is-Table .Table-Cell {
      display: table-cell;
      vertical-align: middle;
    }
    .is-Table .Center-Block {
      width: 50%;
      margin: 0 auto;
    }

FlexBox

参考文章：[Designing CSS Layouts With Flexbox Is As Easy As Pie](http://coding.smashingmagazine.com/2013/05/22/centering-elements-with-flexbox/)

    .Pos-Container.is-Flexbox {
      display: -webkit-box;
      display: -moz-box;
      display: -ms-flexbox;
      display: -webkit-flex;
      display: flex;
      -webkit-box-align: center;
         -moz-box-align: center;
         -ms-flex-align: center;
      -webkit-align-items: center;
              align-items: center;
      -webkit-box-pack: center;
         -moz-box-pack: center;
         -ms-flex-pack: center;
      -webkit-justify-content: center;
              justify-content: center;
    }

**参考资料：**

    [Absolute Horizontal And Vertical Centering In CSS](http://coding.smashingmagazine.com/2013/08/09/absolute-horizontal-vertical-centering-css/)
    [Absolute Centering in CSS](http://codepen.io/shshaw/full/gEiDt)
    [CENTERING ALL THE DIRECTIONS](http://html5hub.com/centering-all-the-directions/#i.n590rnl9he4uud)
    [Seven Ways of Centering With CSS](http://demosthenes.info/blog/723/Six-Ways-of-Centering-With-CSS)
    [How to Center Anything With CSS](http://designshack.net/articles/css/how-to-center-anything-with-css)
    [Vertical Centering With CSS](http://blog.themeforest.net/tutorials/vertical-centering-with-css/)
