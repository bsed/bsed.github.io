---
title: "生成二维码的 jQuery 插件：jquery.qrcode.js"
categories: [ "JS" ]
tags: [ "qrcode" ]
draft: false
slug: "to-generate-a-twodimensional-code-jquery-plugin-jqueryqrcodejs"
date: "2014-06-10 17:25:00"
---

[jquery.qrcode.js](http://jeromeetienne.github.com/jquery-qrcode) 是一个纯浏览器 生成 QRcode 的 jQuery 插件，它使用非常简单，生成的 QRcode 无需下载图片，并且不依赖第三方服务，比如最近 Google 服务在国内访问不稳就造成我好几个网站的 QRcode 不能使用，并且压缩之后大小小于 4K。
## jquery.qrcode.js 使用 ##


<!--more-->


1. 加载 jQuery 和 jquery.qrcode.js：

    <script type='text/javascript' src='http://cdn.staticfile.org/jquery/2.1.1/jquery.min.js'></script>
    <script type="text/javascript" src="http://cdn.staticfile.org/jquery.qrcode/1.0/jquery.qrcode.min.js"></script>

2. 创建一个用于包含 QRcode 图片的 DOM 元素，比如 div：

    <div id="qrcode"></div>

3. 然后通过下面代码生成 QRcode：

    jQuery('#qrcode').qrcode("http://blog.wpjam.com");

4. 默认生成的二维码大小是 256×256，当然可以自定义大小：

    jQuery('#qrcode').qrcode({width: 64,height: 64,text: "http://blog.wpjam.com"});

## jquery.qrcode.js 演示 ##
1. Canvas 方式渲染
![下载.png][1]

2. Table 方式渲染，并加了 1px 描边。
![下载 (1).png][2]


  [1]: https://imgs.gnux.cn/usr/uploads/2015/01/501965241.png
  [2]: https://imgs.gnux.cn/usr/uploads/2015/01/3593721691.png