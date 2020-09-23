---
title: 如何使用margin和padding
date: 2010-11-09 15:48:00
updated: 2014-12-25 15:53:46
tags: 
- shadowsocks5
categories: 
- default

---
margin和padding的意义相信大家都很清楚，可是在具体应用中， 
到底应该使用哪一个，就比较难于判断了。 [这篇文章](http://coliss.com/articles/build-websites/operation/css/901.html) 说得挺清楚的，在这里翻译一下，供参考。

**何时应当使用margin**

 - 需要在border外侧添加空白时。

 - 空白处不需要背景（色）时。

 - 上下相连的两个盒子之间的空白，需要相互抵消时。如15px + 20px的margin，将得到20px的空白。


**何时应当时用padding**

 - 需要在border内测添加空白时。

 - 空白处需要背景（色）时。

 - 上下相连的两个盒子之间的空白，希望等于两者之和时。如15px + 20px的padding，将得到35px的空白。

##  浏览器兼容性问题  ##

在IE 5.x、IE6中，为float的盒子指定margin时，左侧margin可能会变成两倍的宽度。 通过改用padding或指定盒子为`display:inline`可以解决。