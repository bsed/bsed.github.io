---
title: 纯div+css实现圆角属性（兼容IE）
date: 2012-05-02 10:51:00
updated: 2015-03-02 11:16:56
tags: 
- php
- functions
categories: 
- php

---
我们知道div是一个块级元素，可以利用多个div实现块级的堆叠，举个例子就像是蛋糕一样，多层的那种。所以本文讲的实现圆角的方法，原理就是div层的堆叠。


<!--more-->


上代码：

    <html>
    <head>
    <style>
    #a{border-left:1px #333 solid;border-right:1px #333 solid;width:200px;height:200px;}
    .b{height:1px;overflow:hidden;border-left:1px #333 solid;border-right:1px #333 solid;}
    </style>
    </head>
    <body>
    <div class="b" style="margin-left:3px;width:194px;background:#333"></div>
    <div class="b" style="margin-left:2px;width:196px;"></div>
    <div class="b" style="margin-left:1px;width:198px"></div>
    <div id="a">圆角测试</div>
    <div class="b" style="margin-left:1px;width:198px;"></div>
    <div class="b" style="margin-left:2px;width:196px;"></div>
    <div class="b" style="margin-left:3px;width:194px;background:#333"></div>
    </body>
    </html>

运行:[border-radius][1]
代码很简单，a层为内容层，向外以2像素的宽度递减。最外一层要加上background属性，目的是要将上、下两线条呈现出来。

这里实现了3像素的圆角边框，b层的数量决定了要实现多少个像素边框。建议不要多于3层，最好是2层，即2像素圆角边框，或者1层，因为层数越多，圆角的表现就越不圆滑。在firefox  IE6 都通过测试

截图对比一下CSS3的原生border-radius、使用脚本和块级递增减（本文）
![20121025150838.jpg][2]
![20121025151218.jpg][3]
![20121025151433.jpg][4]
从上面的截图就可以看出本文的方法还是不够完美，无法做到圆滑效果，和标准CSS3和挂载脚本相比还是差一点。

但是此方法对于要求不高但是又需要圆角效果的朋友来说，也算是一个不错的选择。


  [1]: https://imgs.gnux.cn/usr/uploads/2015/03/1811219838.html
  [2]: https://imgs.gnux.cn/usr/uploads/2015/03/2336535481.jpg
  [3]: https://imgs.gnux.cn/usr/uploads/2015/03/4156293741.jpg
  [4]: https://imgs.gnux.cn/usr/uploads/2015/03/1831552933.jpg