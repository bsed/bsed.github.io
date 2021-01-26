---
title: "cufon，在网页上画出特殊字体"
categories: [ "JS" ]
tags: [ "cufon" ]
draft: false
slug: "web-font-cufon"
date: "2012-07-03 15:22:00"
---

设计师们有时会使用特殊字体让网页更好看，但浏览器通常只支持Arial、Helvetica等通用字体。那么通常的解决办法就是将特殊字体做成图片。如果要动态生成文字内容怎么办？那也许只有使用CSS3的@font-face功能了……等下！其实还有[cufon](http://cufon.shoqolate.com/)这个工具，可以帮我们在网页上"画"出特殊字体。


<!--more-->


cufon的原理是将TTF字体转换成JavaScript代码，然后在需要特殊字体的地方创建一个`<canvas>`元素，将字体画出来。效果如下：
![cufon.png][1]
使用方法也很简单，只需访问[cufon](http://cufon.shoqolate.com/)，点击Download下载`cufon-yui.js`(目前版本为1.09i)。再点击Generator，上传你的字体文件，即可得到转换后的js文件（假设文件名为yourfont.font.js）。

然后在代码里引入这两个js文件，再写一点简单的代码：

    <head>
    <script src="cufon-yui.js"></script>
    <script src="yourfont.font.js"></script>
    <script>
    Cufon.replace('h1', { color: '#333', textShadow: '2px 2px #ddd' });
    </script>
    </head>

这样h1的内容就被替换成了所需的字体。

更详细的讨论可以[参考使用Cufon渲染网页字体](http://www.ibm.com/developerworks/cn/web/0911_zhuzh_cufon/)。

  [1]: https://imgs.gnux.cn/usr/uploads/2014/12/841588013.png