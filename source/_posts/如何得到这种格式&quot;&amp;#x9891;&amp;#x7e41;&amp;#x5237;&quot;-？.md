---
title: "如何得到这种格式&quot;&amp;#x9891;&amp;#x7e41;&amp;#x5237;&quot; ？"
categories: [ "PHP" ]
tags: [ "php","格式" ]
draft: false
slug: "php-function-format-special"
date: "2011-12-14 17:31:00"
---

1. 新建一个php文件，复制一下内容
```
    <?php
    mb_internal_encoding('utf-8');   // 这里的utf-8是你网站编码，也就是当前文件的编码
    mb_http_output('HTML-ENTITIES');
    ob_start('mb_output_handler');
    echo '撒旦法撒旦法阿斯顿啊';
    ?>
```
<!--more-->


2. 打开浏览器访问此php文件
3. 打开所谓的“抓包工具”（这里用的ie8，选择的工具是 httpwatch professional，也可以用firfox 的firbug）， 然后就会在这个工具的控制台看到如下图的内容：
```
    HTTP/1.1 200 OK
    Date: Mon, 27 Feb 2012 15:14:51 GMT
    Server: Apache/2.2.6 (Win32) PHP/5.2.5
    X-Powered-By: PHP/5.2.5
    Content-Length: 81
    Keep-Alive: timeout=5, max=100
    Connection: Keep-Alive
    Content-Type: text/html; charset=HTML-ENTITIES
    
    &#25746;&#26086;&#27861;&#25746;&#26086;&#27861;&#38463;&#26031;&#39039;&#21834;
```
最下面一行就是上面的汉字，可以复制的。。
ps : 这个有什么作用呢？这样的编码在任意编码环境下页面都能正常显示。还可以让别人在看你代码的时候有点晕。
