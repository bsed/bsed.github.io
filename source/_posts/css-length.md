---
title: css的长度
date: 2013-09-25 21:00:00
updated: 2015-08-12 17:15:21
tags: 
- IMEI
categories: 
- android

---
CSS 里有不少把长度作为值的属性，盒模型的属性就是明显的例子：[width](http://css-tricks.com/almanac/properties/w/width/), [height](http://css-tricks.com/almanac/properties/h/height/), [margin](http://css-tricks.com/almanac/properties/m/margin/), [padding](http://css-tricks.com/almanac/properties/p/padding/), [border](http://css-tricks.com/almanac/properties/b/border/)。还有其它例子：[box-shadow](http://css-tricks.com/almanac/properties/b/box-shadow/)的偏移和阴影半径，或者字体的大小和字间距。CSS 都有哪些可用的“长度单位”呢？已经相当多啦。
# 绝对长度

## px

    .wrap {
      width: 400px; 
    }

Pixel 可能是最容易想到的衡量“设备像素”的单位，它不需要对显示器上的屏幕像素做任何事。它的确是一个[简单易用的度量尺寸](http://inamidst.com/stuff/notes/csspx)。


<!--more-->


它被设想成一个跨设备和显示器的标准化的值，实际上这一设想越来越不成立。举例来说，在 iPad mini 和 iPad 上[渲染同一个网站](http://cdn.css-tricks.com/wp-content/uploads/2013/03/ipad-mini.png)，如果长度单位都被设成 px ，按理说 iPad mini 上的网站会超出屏幕。

px 尽管一直有些缺点，它仍然是网络上典型的度量单位。许多其它的长度会直接换算成 px，JavaScript 就是用的 px 。
## in

    .wrap {
      width: 4in; 
    }

英寸是物理尺寸，但是在 CSS 里，它直接换算成 px 。欢迎在评论里添加测试用例，我会把它加到文章中，我是一直没找到针对这些物理尺寸的可用的测试用例。

`1in == 96px`

## cm

    .wrap {
      width: 20cm; 
    }

众所周知，厘米是个熟悉且有用的物理尺寸。它们也被换算成 px：

`1cm == 37.8px`

## mm

    .wrap {
      width: 200mm; 
    }

`1mm == 0.1cm == 3.78px`

# 字体相对长度

## Em

    .wrap {
      width: 40em; 
    }

一个相对单位，基于当前字体中的大写字母“M”，起初是作为[排版尺寸](http://en.wikipedia.org/wiki/Em_(typography)) 。尽管当你改变 font-family 时长度没变，它的确是根据 font-size 变的。

完全没有任何 CSS 的话，[1em](http://codepen.io/chriscoyier/pen/vnhup) 将是：

`1em == 16px == 0.17in == 12pt == 1pc == 4.2mm == 0.42cm`

如果任何 CSS 改变字体大小（在文档的任何层级），1em 将等于任何新的 font-size 值。

应用了 font-size 的 em 单位会累乘，因此如果 3 个 `font-size` 是 1.1em 的元素嵌套，结果尺寸是 `1.1 X 1.1 X 1.1 = 1.331 rem（root em）`。意思是虽然一个元素被设定 10em，不是说它的宽度在任何位置都是一成不变的，如果 `font-size` 有变（译者注： 指该元素的父元素的 font-size 改变，如果 font-size : 20px，则 1em = 20px；font-size : 10px 则 1em = 10px。），它可能变宽或变窄（[看证据](http://codepen.io/chriscoyier/pen/HizKe)）。

## Rem

    .wrap {
      width: 40rem; 
    }

像 em 的相对单位，但它总是相对于“root”元素（也就是 :root{}），而不像 em 是相对于级联元素。这就极大简化了相对单位的相关工作（译者注： 只需修改 :root{}里的 font-size 定义，然后应用 rem 的元素自动跟着变）。

**值得注意的浏览器支持问题**：IE 8，Safari 4，iOS 3.2 不支持。（译者注：Can I use）
## Points

    .wrap {
      width: 120pt; 
    }

pt 是物理尺寸，1pt = 1/72 英寸。Points 主要用来定义打印字体的尺寸（很可能这就是 CSS 支持它的原因）。常常听到以下描述：“他们一定要把重要信息设成 很小的 8pt ！”。

pt 最大用途是在实体媒介参与的打印样式中定义字体，但是没人阻止你在屏幕媒介或其它媒介使用 pt ，只要它们支持这一尺寸。

值得注意的浏览器支持问题：在屏幕上呈现 pt 尺寸会有很大的差异。 这是 IE 6 和 Firefox（可能是 3.6）[对照](值得注意的浏览器支持问)

## Pica

    .wrap {
      width: 12pc; 
    }

跟 pt 一样，唯一不同的是 1pc == 12pt。

## ex

    .wrap {
      width: 60ex;
    }

这个尺寸基于当前字体的 x-height。有时基准来源于嵌入的字体信息，有时浏览器通过测量一个小写字型自己指定它，最糟的是，它被设定为 0.5em 。它取名“x”高度，可能当初想以 x 字母的高度为基准吧。了解一下 x-height ，考虑到有些小写字母像“d”有上半部分，x-height 没有上半部分，它是这些字母的下部的高度。

不像 em，当你改变 font-family 时，em 大小不变，ex 单位会发生变化，ex 的值特别受字体的限制。（[实例](http://codepen.io/chriscoyier/pen/aDtdv)）
## ch

    .wrap {
      width: 60ch;
    }

ch 很像 x-height，唯一区别是 ch 以零（0）字符的宽度为基准，font-family 改变它会随之改变。

值得注意的浏览器支持问题：在写这篇文章的时候，没有基于 WebKit 的浏览器支持。

# Viewport 百分比长度

## vw

    .wrap {
      width: 10vw;
    }

这是“viewport（视口） 宽度”单位，1vw 等于 1% 的视口宽度。它跟百分比相似，不同点是这个值跟所有元素保持一致，不管它们的父元素或父元素宽度。有点像 rem 单位总是相对于 root 。

控制打印字体是主要的用例，看这里 [Viewport Sized Typography](http://css-tricks.com/viewport-sized-typography/)。

**值得注意的浏览器支持问题** 除了最新的 iOS 6 任何移动浏览器都不支持。它适用于所有视口相关的长度单位。
## vh

    .wrap {
      width: 10vh;
    }

跟 vw （viewport 宽度）单位相似，但它是基于 viewport 高度的。
## vmin

    .wrap {
      width: 20vmin;
    }

这个值取 vw 或 vh 中较小者。这个单位在打印字体中的作用比在屏幕显示中要大。
## vmax

    .wrap {
      width: 20vmax;
    }

这个值取 vw 或 vh 中较大者。

**值得注意的浏览器支持问题：** 基于 WebKit 的浏览器支持 vmin 但还不支持 vmax 。Firefox 不支持 vmax。

# 特例出现

## 百分比

    .wrap {
      width: 50%; 
    }

长度设成百分比是基于父元素的同一属性的长度。比如，如果一个元素宽度 450px，宽度设成 50% 的子元素将是 225px 宽 [1]。

**备注**：严格来讲百分比不算一个长度单位，但我把它加进来了，因为比较相关。
# 更多信息

    [关于长度的 W3C 规范](http://www.w3.org/TR/css3-values/#lengths)

## 你的浏览器支持哪些呢？

**[看这里](http://codepen.io/chriscoyier/pen/CiwFD)**：

假定子元素不是 inline-level （行内级）或 带有一些怪异的 table-y 的 table cell，或者 flex 子元素，或 grid cell 或者任何其它的稀奇古怪的东东。





