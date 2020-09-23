---
title: IE条件标记失效，如何判断IE10
date: 2015-03-02 12:26:00
updated: 2015-03-02 12:34:49
tags: 
- memcached
- session
categories: 
- php

---
![ie9_not_10.jpg][1]

差不多在一年前，微软已经说明，今后的IE将不再支持使用<if IE>这样的条件判断IE类型（[原文见此](http://blogs.msdn.com/b/ie/archive/2011/07/06/html5-parsing-in-ie10.aspx)）。随着Win8的发布，IE10也将成为用户更新的趋势。虽然IE在众多方面都显得不尽人意，但是至少比坑爹的IE6要强多了。这篇文章就是告诉大家，在IE不断更新也不断让人失望的今天，甚至明天，我们还是要学会写IE hack，第一步就是鉴别IE10。


<!--more-->


回到一开始所说，我们之前用的<if IE>在IE10下已经失效了。

    <!--[if IE]>
    <p>It is a test！</p>
    <![endif]-->

很遗憾，像这样的HTML将被IE10直接无视跳过。因此，找出其它的方案来识别IE10，对于前端开发者来说，需要另辟蹊径（当然，你随时可以借助后端，但那不是前端开发者的事情了）。以下是网络上流传的几条“偏方”。

### @cc_on ###

    <!--[if !IE]><!--<script>
    if (/*@cc_on!@*/false) {
     document.documentElement.className+=' ie10';
    }
    </script><!--<![endif]-->

如此，将为整个HTML的DOM元素增加一个名为ie10的class，于是我们就可以直接在CSS中这样针对IE10开刀：

    .ie10 .example {
     /* 仅限IE10的代码 */
    }

不知道为啥这个恶心的玩意儿怎么来的，但它确实有效。然而这段代码依赖于JavaScript，因此不是我们的最佳选择。
### @media -ms-high-contrast ###
以下是来自Github一则技巧，它利用了IE10对ms-high-contrast这一CSS属性的支持，而IE9恰好不支持：

    @media screen and (-ms-high-contrast: active), (-ms-high-contrast: none) {
     /* 仅限IE10的代码 */
    }

看起来很不错，可惜这个技巧在IE10的早期预览版（Platform Preview 1 and 2）当中并不奏效，因为这些预览版不支持这一CSS属性；而且IE11也无法与IE10区分开了。所以这则技巧也不是我们的最佳选择。
### @media 0/0 ###

    @media screen and (min-width:0\0) {
     /* IE9与IE10的专有代码 */
    }

缺点很明显：它不能避开IE9。但是考虑到Windows 7的用户最终将通过安全更新，将IE9升级为IE10，我们姑且乐观的认为IE9最终就不再存在了。而IE11是否识别这段代码尚未知晓，于是这条技巧八成最适合未来的情形。

### 结论就是没有结论 ###

结论就是没有结论。上面这几条技巧其实没有一个万全之策，但实际上和IE10死磕的情形不多。我们应该做的是使用一些正规的手段检测浏览器对我们需要特性的支持，比如使用之前说过的Modernizr。


  [1]: https://imgs.gnux.cn/usr/uploads/2015/03/1852963317.jpg