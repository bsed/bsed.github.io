---
title: "CSS旋转与翻转小计"
categories: [ "Css" ]
tags: [ "css2","rotate","transform" ]
draft: false
slug: "css-transform-rotation"
date: "2012-12-01 18:06:00"
---

css 2.0还是没有翻转的，3.0里面有rotate属性，这个可以把元素进行任意角度旋转，灰常强大。除了这个rotate，还有一个scale，一般用法格式是


<!--more-->


-moz-transform:scale(1，1);

括弧里面（1，1）前者表示X轴，后者表示Y轴，当数字大于1时放大，大于0并小于1时缩小，很好理解，那么负数是怎样的效果？答案是 翻转 。
-moz-transform:scale(-1，1);

表示水平翻转；

-moz-transform:scale(1，-1); 

表示垂直翻转。

不过这些都是moz或者webkit的，万恶的IE怎么办? 
于是我们想到滤镜，滤镜里面有这么一堆东西：

顺时针旋转图片90度
picID.style.filter="progid:DXImageTransform.Microsoft.BasicImage(rotation=1)";
旋转180度
picID.style.filter="progid:DXImageTransform.Microsoft.BasicImage(rotation=2)"; 
逆时针旋转90度
picID.style.filter="progid:DXImageTransform.Microsoft.BasicImage(rotation=3)";

有没有想问“rotation=4”什么效果? 面壁去...90、180、270都出来了，还要rotation=4实现360干嘛，感觉这个很费呢。不过这个是静态费，如果动态的话，这个就是必须的了。假如用js控制元素旋转，从0顺时针旋到270的时候，如果没有360的话，那么270会快速的逆时针回到0，这样就2了，所以要给个360过渡，让270自然到360，然后再循环，这样就流畅了……

这跟css3的rotate差不多，不过只能固定角度的旋转，来个顺时针15度就没折了，css确实很轻松的transform：rotate(15deg);

不过这样也只是实现了“旋转”，还有“翻转”没实现。IE的翻转就需要用到这个：

水平翻转：filter:FlipH; 
垂直翻转：filter:FlipV; 

这样就齐全了，不过是否还有疑问，为什么要写这《CSS旋转与翻转》，有何实战意义没?

虽然我还没有在实际的项目应用到，不过我有这么一个想法，一般网站都有对称性按钮，图片背景之类的元素，我们一般处理方式就是逐个切出加以定位，但是如果运用旋转翻转的话，那么就只需要切一份，其它如果有如90旋转，水平翻转，垂直翻转的图片的话，那么就用css来写，这样就用css代替了ps的图片操作，节省了多余图片，提高网速。

以gotrip页面来看：
![gotrip.png][1]
横竖版切换按钮可以用旋转90度来实现，从而节省2张图片
![zxyj.png][2]
首页标题部分左右箭头可以用180度旋转或者垂直翻转来实现，横版的时候就用180度旋转或者水平翻转。
以淘宝来看：
![tabao.png][3]
小菜单的黑色小三角，可以用180度旋转或者垂直翻转来实现；
![tabaosd.png][4]
信息提示，图片展示部分切换按钮可以用180度旋转或者水平翻转。
具体代码：
1、水平翻转

    -moz-transform:scale(-1，1);
    -webkit-transform:scale(-1，1);
    -o-transform:scale(-1，1);
    transform:scale(-1，1);
    filter:FlipH; 

2、垂直翻转

    -moz-transform:scale(1，-1);
    -webkit-transform:scale(1，-1);
    -o-transform:scale(1，-1);
    transform:scale(1，-1);
    filter:FlipV; 

3、顺时针旋转90度

    -moz-transform:rotate(90deg);
    -webkit-transform:rotate(90deg);
    -o-transform:rotate(90deg);
    transform:rotate(90deg); 
    filter:progid:DXImageTransform.Microsoft.BasicImage(rotation=1);

其它的我就不一一列举了

虽然这些都是细节部分，感觉就是省也省不了多少，但是周五晚我收到“鬼”给我的邮件回复，里面一句很瞩目的话：细节体现专业。我反反复复把邮件看了几遍，那晚躺在床上想模块，想细节，差点睡不着；
网站都能搭建出来，但是框架模块搭建的粒度，细节，重用率，直接反映了重构者的水平，这就是一个看似相同的网站，体现出来的水平境界不同。
也许要看一个重构的家伙实力如何，就是在细节一较高下。

所以不要以为把网站任务式的做出来就完事，里面还有好多需要注意需要优化的地方，就看你水平的高低去发现去优化。

我的留言旋转代码：
这个要用css3，ie要用滤镜，举例顺时针旋转90度

    -moz-transform:rotate(90deg); 
    -webkit-transform:rotate(90deg);
    transform:rotate(90deg);
    filter:progid:DXImageTransform.Microsoft.BasicImage(rotation=1);

上面代码前三行是css3，第四行是ie滤镜的简单的90度的整数倍旋转方式，更加复杂的度数要用矩阵，需要的话可以查查资料
注：左旋把90改为-90，rotation=1改为rotation=3.
注：如果你的层上还有文字什么的，也会被旋转，所以有文字的话，和背景图放在不同的层上，旋转有背景图的层

  [1]: https://imgs.gnux.cn/usr/uploads/2014/11/3202620370.png
  [2]: https://imgs.gnux.cn/usr/uploads/2014/11/1114264804.png
  [3]: https://imgs.gnux.cn/usr/uploads/2014/11/1469774219.png
  [4]: https://imgs.gnux.cn/usr/uploads/2014/11/2520901425.png