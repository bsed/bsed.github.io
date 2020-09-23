---
title: 让ie6,ie7,ie8很好的支持css3
date: 2014-09-03 13:38:00
updated: 2014-09-07 11:58:04
tags: 
- clib
- github
categories: 
- default

---
我们都知道IE6，7并不支持CSS3的属性，IE8也不能很好的支持CSS3。但是有一个小脚本能够做到，它可以让IE支持 CSS3，包括：border-radius (rounded), box-shadow ( shadow), text-shadow等……
 
做前端的同学都应该听说或者用过，是一段脚本，可以让ie实现css3里的圆角和阴影效果。

css带来的便利是很容易感受的到的，但恶心的是它在ie下的不兼容，所以某位牛人现身写了个ie-css3.htc，允许你在ie下去使用css3的部分东西。

ie-css3的使用方法很简单，在你需要使用css3的样式里加入behavior: url(js/ie-css3.htc);就可以了（括号里是ie-css3.htc的地址）
ie-css3.htc http://fetchak.com/ie-css3/
<!--more-->


    .box {
      -moz-border-radius: 15px; /* Firefox */
      -webkit-border-radius: 15px; /* Safari and Chrome */
      border-radius: 15px; /* Opera 10.5+, future browsers, and now also Internet Explorer 6+ using IE-CSS3 */
      -moz-box-shadow: 10px 10px 20px #000; /* Firefox */
      -webkit-box-shadow: 10px 10px 20px #000; /* Safari and Chrome */
      box-shadow: 10px 10px 20px #000; /* Opera 10.5+, future browsers and IE6+ using IE-CSS3 */
      behavior: url(ie-css3.htc); /* This lets IE know to call the script on all elements which get the 'box' class */
    }

下载下载增强版ie-css3.htc：http://files.cnblogs.com/aiyuchen/ie-css3.htc.zip
点击查看详情：http://fetchak.com/ie-css3/

另外也有个CSS3 PIE的小脚本也可以同样让IE6－IE8支持CSS3，原理跟IE－CSS3相似。项目地址：http://css3pie.com/下载地址：http://github.com/lojjic/PIE/downloads
 
如果你需要一个支持CSS3 的box content，你需要添加一行代码：
behavior: url(ie-css3.htc);
代码：