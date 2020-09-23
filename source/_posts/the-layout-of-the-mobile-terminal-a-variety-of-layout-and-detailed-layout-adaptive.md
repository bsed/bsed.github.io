---
title: 移动端布局-各种布局方式及自适应布局详解
date: 2016-02-23 21:21:00
updated: 2016-02-27 11:40:01
tags: 
- debug
- atom
- lldb
- swift
categories: 
- ios

---
> 移动web页面该采取什么方式布局？用响应式？用自适应？……这是很多前端新手会问到的问题，其实就目前而言，移动端还没有一套标准，毕竟各大浏览器对html5,css3支持表现都不同。so～你采取怎样的布局，没人敢说你这样是不对的，只会跟你说:这样用户体验不好吧？要不你再改改…

那么问题来了，挖掘机哪家强？咳咳……那该用哪一种布局方式？我个人的理解是:1、用户体验好的；2、可以提高开发效率的。所以我建议是采用自适应布局，它能很好可以满足这两个条件。
## 布局方式
先来说说目前国内各大网站都采取什么样的布局方式，大概有以下三种：
### 1、常规式

[http://sina.cn/?wm=4007](http://sina.cn/?wm=4007)
[http://m.sohu.com/](http://m.sohu.com/)
[http://xuan.3g.cn/?fr=sm1](http://xuan.3g.cn/?fr=sm1)

### 2、响应式

[http://www.samsung.com/cn/home/](http://www.samsung.com/cn/home/)
[http://www.bootcss.com/](http://www.bootcss.com/)
### 3、自适应

[http://harmay.com/mIndex](http://harmay.com/mIndex)
[http://520ued.com/global/rem-phone.html](http://520ued.com/global/rem-phone.html)
[http://wapgd.189.cn/TS/?ccmpid=wttz](http://wapgd.189.cn/TS/?ccmpid=wttz)
[http://m.mi.com/v2.html](http://m.mi.com/v2.html)


<!--more-->


第一种我也不知该怎么叫，因为它的布局方式很怪。其实仔细分析，它采用的布局很简单，选各个分辨率下比较适中的字体大小，百分比宽度，左右浮动布局。这个可以做到在移动端各个分辨率下保持相同效果。这种布局的优点是简单，只需要图片做一下自适应处理，就跟pc端没两样，缺点是：用户体验不好，用于复杂的布局是时就不能保证各个分辨下的屏幕一致性了。

第二种响应式虽然是很早很早就提出来了，但是国内用这种布局的不多，毕竟响应式要考虑的东西实在太多，对于国内普遍追求的是开发效率至上的来说，采用这种布局的不多，国外的比较多。

第三种就是我所推荐的方式，淘宝、小米等大型电商手机官网都是采用这种方式。优点：能够较好的保持各个浏览器整体布局的稳定，无论是字体、图片、背景图片都能随着屏幕大小变化而变化；缺点：图片拉伸会变形~

**采用自适应的布局方式主要解决四个问题：1、文字；2、图片；3、背景图片；4、css sprite；**

一、文字的设置；

总观自适应的文字设置方式大概有三种：a、js控制（[web app 变革之rem](http://520ued.com/article/549125815f85b6b44ca20b2b)）；b、通过em控制（小米、深圳电信手机官网）；c、通过calc()和vw控制；

a：实现的方式大家研究一下web app 变革之rem的文章就知道，我不多做介绍。目前首推是这种方式。
b：em的布局方式很早就已经提出，百度也会有相当多的教程，只是我个人觉得em对于嵌套多层父级时，需要测算的东西就麻烦的多 。下面是小米采用em布局的方式，首先通过定义好不同分辨率的字体值，其他数值的设置基本采用em。
![mobile-layout_01.png][1]
![mobile-layout_02.png][2]

c:这种方式需要大家提前了解几个css3属性，分别是：rem、calc（）、vw。rem我就不介绍，问度娘。重点说说calc（）和vw。从这个浏览器兼容性 网站可看到，现代浏览器对这两个属性还是可以，而且苹果手机都是支持的，主要问题是在安卓浏览器上，它们在低版本的安卓系统不支持，然后大家再看看友盟数据的统计  安卓各系统所占的比例。基本上如果是现在买手机都是安卓4.4以上的。那这个到底能不能用呢？看情况，如果你的老板说，我们的目标是：要兼容所有安卓系统，这个也有办法解决，下面会说到。
![mobile-layout_03.png][3]

![mobile-layout_04.png][4]

## 自适应布局详解
### 一、字体设置

字体自适应代码：
方法一：

```html
    html { font-size: 100px; font-size: calc(1000vw/32); }
    body { font-siez:.12em; }
```
calc()：表示可以在里面进行数值的运算，而vw：换算公式有点麻烦，首先：(设定值x窗口大小)/100；
比如设置：h1:{font-size:10vw;}，当手机320宽度时：(10×320)/100=32px；当手机6400宽度时：(10×640)/100=64px；

font-size: calc(1000vw/32)这段代码的意思：

> 当屏幕320的时候：(1000*320)/100/32=100px;
> 当屏幕640的时候：(1000*640)/100/32=200px;


当屏幕变化时，该怎么把这个动态的数值赋给fon-size呢？这时就轮到calc()出场了，calc()里面的数值都可以就行运算，刚 好可以达到与js一致的效果，随屏幕大小变化而变化。好了，这个明白了，那为什么html{font-size:100px},body{font- size:0.12em}需要这样设置呢？这个也就相当于我们平时的样式重置，我们在做pc端一般会将字体设置为12px，我们既然是要把html字体的 大小设为100px，万一引入其他不是自己写得代码，那它的字体就变成100px，这么大肯定不行，然后body设置body{font-size:0.12em}，也就是相对于他的父级html的100*0.12=12px，也就是body的字体大小为12px，避免其他引入的东西会变得不正常。

但是这种方法虽好，却总会出现一些小问题，字体显示效果PC模拟器跟真机测试还是有差距~你若问为什么会出现这样的问题，我只能说是安卓的机型实在太多了，而且各浏览器渲染效果又有差异，是无法实现任何机型都保持一样的效果。这种方法我觉得还是待定吧，期待以后会上得。

### 方法二：

js代码：

```js
    (function (doc, win) {
    var docEl = doc.documentElement,
    resizeEvt = ‘orientationchange’ in window ? ‘orientationchange’ : ‘resize’,
    recalc = function () {
    var clientWidth = docEl.clientWidth;
    if (!clientWidth) return;
    docEl.style.fontSize = 100* (clientWidth / 320) + ‘px’;
    };
    if (!doc.addEventListener) return;
    win.addEventListener(resizeEvt, recalc, false);
    doc.addEventListener(‘DOMContentLoaded’, recalc, false);
    })(document, window);
```
css代码：

```html

    html { font-size: 100px;}
    body { font-size:.12em｝
    
    @media all and (max-width:320px) {
    html { font-size: 100px!important; }
    }

```
请不要漏掉上面的代码，否则会出现问题。看看[demo的效果](http://codeliker.com/mobileDemo/)。上面我为了代码，我的demo还设置了屏幕640的媒体查询，只是为了方便大家看，去掉也不会有影响。

### 二、图片设置

好了，解决上面字体问题，下面的一切都好办，所有的都依赖单位rem来变化就ok~
大家都知道当给图片加上width:100%，图片就会自适应，但是这种情况只是用于图片作为一个整体，左右没有信息存在，下面的情况就不能用width：100%来处理了,因为图片会占据整块，把文字信息挤下去

具体设置方法，看[demo](http://codeliker.com/mobileDemo/),在demo里面我说得很详细了

### 三、背景图片设置
自适应的背景图片比较简单，自需要加多一个background-size:100% 图片高度/2；属性就可以了，不懂这属性问度娘去~
需要注意的一点是：当背景图片不填满整个容器时,background-size:图片宽度/2 图片高度/2；

四、css sprite设置
手机端无疑是更需要节约流量，图片尽量要少，没有为什么，以为国内流量费就是贵得坑！
需要注意的一点是：这里background-size:整张csssprite图片宽度/2 整张csssprite图片高度/2；

好了,手机端的自适应核心问题都解决了，更多详细的布局细节我无法一一讲到，我平时遇到问题多调试几次也就解决。有人会问为什宽度要用百分比%？用rem行不行？大家先看这网站，它采用所有数值都是rem设置，火狐是没问题的，但是谷歌有问题了,在分辨率320下时，有些内容没法显示出来了，这个只是在pc浏览器模拟器上的，真机我很难找到320的手机来测试，总之谷歌对rem似乎还是有点差异，所以我建议用100%
![mobile-layout_06.png][5]

![mobile-layout_07.png][6]

原文: [http://codeliker.com/mobile-layout-b/](http://codeliker.com/mobile-layout-b/)

  [1]: https://imgs.gnux.cn/usr/uploads/2016/02/144183200.png
  [2]: https://imgs.gnux.cn/usr/uploads/2016/02/1971614286.png
  [3]: https://imgs.gnux.cn/usr/uploads/2016/02/2142437360.png
  [4]: https://imgs.gnux.cn/usr/uploads/2016/02/4154305567.png
  [5]: https://imgs.gnux.cn/usr/uploads/2016/02/2059857574.png
  [6]: https://imgs.gnux.cn/usr/uploads/2016/02/1222773427.png