---
title: "让你的网站加载更快,优化你的css"
categories: [ "Css" ]
tags: [ "css","优化" ]
draft: false
slug: "make-your-website-load-faster-optimize-your-css"
date: "2014-12-18 16:52:00"
---

这篇文章是在其他地方看到的。就在这一个网站看到过。
但是今天发现网站打不开了，是一个百度的二级域名，好像是百度前端小组什么的，我发现百度快照还可以看。
我赶紧转载一份过来，省的以后没办法查看了。我觉得他说非常不错。

优化你的css，是优化你的xxx系列的第一篇，后续会推出多篇，欢迎大家来关注移动云前端小组获取最新文章。

## 系列概述 ##
在移动web兴起的年代，速度优化重新被大家重视起来，因为手机的网络环境和性能比PC端差了很多，估计大家也能感觉到用手机打开网页的时候，能明显感觉到页面蜗牛般的速度。

这个系列的优化会以移动环境为基础，当然绝大多数规则也同样适合PC端。
## 优化的基本原则 ##
速度优化有一些基本思路，提前总结一下

按需加载(只加载你需要的)
并行(让串行的事情并行起来)
压缩(通过压缩减少体积)
缓存(利用缓存，减少请求等待)
预测(预测用户行为，提前发出请求)
合并(把多个零散文件合并起来，减少请求)
自动化(让速度优化变成一种常规，和自动化工具(例如gulp,grunt,fis)结合，减少成本)

## 进入正题，优化你的css ##
为什么第一篇讲css，因为css是最难优化的，图片和js你都可以延迟加载，而css不可以，你必须在dom前面加载css，你必须接受css阻塞dom渲染的现实。
## css优化之压缩(cssshrink) ##
我们一般都会对css进行常规压缩，主要做去空格和换行的工作。这里推荐的cssshrink会做更精细的工作，cssshrink会首先通过css parser对css进行解析，然后有针对性的进行优化。例如会吧0px和0%转换成0，bold转换成700，字符级别的极致压缩，为作者点个赞。

cssshrink具体的优化策略，[点此查看](http://cssshrink.com/velocity)
cssshrink [GitHub地址](https://github.com/stoyan/cssshrink)

## css优化之合并 ##
使用gulp-concat将多个css合并在一起
不要使用@import 减少阻塞和请求
## css拆分 ##
看起来和上面有些冲突，这也是css和其它部分优化不同的地方。 一般我们大家都习惯把css放在最上面，js放在最下面。这是一个好习惯，但是对于css来说并不是最好的选择。

在移动端，大家非常重视首屏时间，也就是用户看到页面的时间。把整个页面的css都放在最上面，大量首屏用不到的css会阻塞首屏的展现。

head只放首屏能用到的css，首屏外的css下移
## css使用率 ##
一般页面经过多人维护后，会产生大量用不到css，大家也不敢随意删除，这就需要一些检测工具

[unu](https://github.com/demohi/unu)

1.1 unu是一个用来检测页面哪些css没有用到的Node.js模块

1.2 优点：提供[可视化界面](https://github.com/demohi/unu-web),使用非常简单，输入url，即可查看页面css的使用情况

1.3 缺点：目前只支持style标签式的css，另外没有执行页面的js

[uncss](https://github.com/giakki/uncss)

2.1 uncss是可以把页面css没有用到去除的模块

2.2 优点：支持命令行和gulp、grunt插件，支持link方式，基于phantomjs，模拟浏览器执行，支持js执行

2.3 缺点：仅凭一个url导出的css，不具有实际价值，另外不支持style标签

[critical](https://github.com/addyosmani/critical)

3.1 critical是一个用来检测首屏css有哪些没用到的模块

3.2优点：可以输入首屏宽高来检测、有gulp、grunt插件

3.3 缺点：不支持url，只支持本地html，不支持style标签
## 总结 ##

速度优化对于开发人员来说是件降低生产力的事情，所以需要尽可能的自动化，设置好规则，无痛优化，同时避免后续恶化。