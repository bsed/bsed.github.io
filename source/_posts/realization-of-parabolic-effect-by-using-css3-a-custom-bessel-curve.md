---
title: 使用CSS3实现抛物线效果--自定义贝塞尔曲线
date: 2015-09-28 19:40:00
updated: 2016-06-02 11:29:01
tags: 
- oracle
- plsql
categories: 
- java

---
> 原文：http://stoneydream.com/2015/08/02/%E4%BD%BF%E7%94%A8css3%E5%AE%9E%E7%8E%B0%E6%8A%9B%E7%89%A9%E7%BA%BF%E6%95%88%E6%9E%9C-%E8%87%AA%E5%AE%9A%E4%B9%89%E8%B4%9D%E5%A1%9E%E5%B0%94%E6%9B%B2%E7%BA%BF/
> 作者：stoneydream 

这周参与公司的一个ios Hybrid App项目，需要实现一个加入购物车时的抛物线效果。乍一看，似乎没有头绪，之前制作的CSS3动画，基本都是“直线型”的，无非在移动速率上做些效果，并没有尝试过这类“曲线”类的动画。回想起中学物理，抛物线运动可以分解为水平速度和垂直速度，水平方向是匀速的，垂直方向是加速的。于是我们只需“同时”定义这两个方向的动画即可实现抛物线。google了一番，广大同仁们的做法也是类似。给出一张分解图：
图片丢失:>
首先对我启发比较大的是 [CSS3动画-抛物线运动](http://blog.csdn.net/boycycyzero/article/details/44088707) 这篇文章（在此表示感谢）。然而，这位同仁实现的抛物线的初速度是水平的，我需要实现一个先上扬再下坠的过程，即斜上抛。于是我又陷入沉思，继续google之，发现了这个 [神器](http://jeremyckahn.github.io/stylie/) ，可以预览最终的移动轨迹，后来在张鑫旭的博客中发现了[又一个神器](http://cubic-bezier.com/)（张鑫旭确实是一个能折腾的大神，在此也表示感谢）.这两个“神器”摆在一起，我当时却糊涂了。其实chrome开发者工具中也有类似的内置曲线图：

<!--more-->

也一直对其犯迷糊。后来我恍然大悟，这不就是一个时间路程图嘛！
图片丢失:>
只不过，由起点(0,0)，终点(1,1)，活动点1(x1,y1)，活动点2(x2,y2)四个点决定这个贝塞尔曲线走向，用于描述**“一个维度”的动画完成的过程，千万不要与我们这里的抛物线的轨迹混淆！我之前就是卡在了这个误区！**

再回到我们的抛物线问题，我们假定使用定位方式的 left/top来实现抛物线。

水平方向是匀速的，那么left属性值动画的贝塞尔曲线应该是这样的：
图片丢失:>
垂直方向top属性就复杂一点了，首先要有一个向反方向减速的过程，直到速度为0，再向正方向加速，即垂直向上扔一个物体一样：
图片丢失:>

关键就是那段负值的曲线，才能够实现“斜上抛”的效果。

定义好动画函数（transition-timing-function）后，剩下的事就简单了，只要给出“起抛坐标”与“抛落终点坐标”就行了。我们先采用前面假定的定位方式（fixed或absolute）指定起点与终点，实现一个版本：

[http://runjs.cn/detail/qz59odqv](http://runjs.cn/detail/qz59odqv)

关键行：

    div.animate {
      -webkit-transition: left 0.5s linear, top 0.5s cubic-bezier(0.5, -0.5, 1, 1);
              transition: left 0.5s linear, top 0.5s cubic-bezier(0.5, -0.5, 1, 1);
      left: 300px;
      top: 300px;
    }

在PC上效果还算不错，这时想到CSS3里还有个属性可以改变位置，那就是transform:translate/translateX/translateY。

两者有什么区别呢？

且看这篇文章 [Why Moving Elements With Translate() Is Better Than Pos:abs Top/left](http://www.paulirish.com/2012/why-moving-elements-with-translate-is-better-than-posabs-topleft/) ，也是刚刚google到的\(^o^)/~。

*总结下来：*

尽可能的使用CSS3实现动画，浏览器会帮助我们优化
如果不能避免基于javascript的动画，避免使用`setTimeout`, `setInterval`，尽量使用requestAnimationFrame，浏览器同样会优化这个过程
使用2D transforms代替定位方式（translate能够实现“亚像素”，定位则不能，最小单位为1px），将带来更高的FPS与更少的重绘，使动画更流畅
借助开发者工具帮助我们分析动画性能
那么接下来，换用translate的方式再实现一遍，由于left和right是两个独立的CSS属性，可以在`**transition中设置两组不同的timing-function值，应用于同一个元素，而translateX和translateY同属于transiform这个CSS属性，无法设置不同的timing-function应用于同一元素，所以这种方式需要额外引入一个元素：**`

http://runjs.cn/detail/gjhlxwnz
