---
title: "使用CSS3 font-face实现自定义字体"
categories: [ "Css" ]
tags: [ "css3","font-face" ]
draft: false
slug: "custom-fonts-using-fontface-css3"
date: "2012-12-04 13:39:00"
---

过去几十年网页的字体大部分都局限于系统安装的字体，如果设计稿中一些不是系统字体，大部分都会使用图片方法去呈现在网页上。
`CSS3 font-face`的出现，将改变这一切，我们可以在网页中使用我们喜欢的任何字体。

# 上传字体

首页确认字体是否有网络使用许可证。去免费字体的网站可以下载到很多拥有使用许可证的字体。
接下来把字体上传到你的服务器上边。建议可以将文件上传于fonts的文件夹中。
字体文件包含的类型包括：


<!--more-->


    TTF或OTF，适用于Firefox 3.5、Safari、Opera
    EOT，适用于Internet Explorer 4.0+
    SVG，适用于Chrome、IPhone

多种字体类型生成工具：[http://www.fontsquirrel.com/fontface/generator](http://www.fontsquirrel.com/fontface/generator)

# 向CSS代码中添加font-face部分

首先添加对font-face的声明

    @font-face{
    }

其次在font-face中添加字体姓名和字体位置，根据不同浏览器对字体的支持情况加载不同类型的字体。

    @font-face{
     font-family:'lovelyFont';
     src:url('fonts/lovely_font.eot');
     src:url('fonts/lovely_font.otf');
     src:url('fonts/lovely_font.ttf');
    }

字体还有个类型，如果让定义更加规范，建议带上字体的类型声明。

    @font-face{
     font-family:'lovelyFont';
     src:url('fonts/lovely_font.eot');
     src:url('fonts/lovely_font.otf') format('opentype');
     src:url('fonts/lovely_font.ttf') format('truetype');
    }

避免兼容字体写法，重复加载字体，加上local判断。即字体本机已经存在，使用当前字体，如果没有加载字体文件。

    @font-face{
     font-family:'lovelyFont';
     src:url('fonts/lovely_font.eot');
     src:local('Lovely Font'),local('Lovely-Font'),url('fonts/lovely_font.otf') format('opentype');
     src:local('Lovely Font'),local('Lovely-Font'),url('fonts/lovely_font.ttf') format('truetype');
    }

# 页面元素加载字体

接下来就是给一个特定的元素或一组元素加入对特定字体的定义。

    div { font-family: 'lovelyFont', sans-serif; }

# 字体变体

如果需要统一加载的是加粗的字体或者是斜体的字体，我们只需要把相关的属性写到font-face里边即可。

    @font-face{
     font-family:'lovelyFont';
     src:url('fonts/lovely_font.eot');
     src:local('Lovely Font'),local('Lovely-Font'),url('fonts/lovely_font.otf') format('opentype');
     src:local('Lovely Font'),local('Lovely-Font'),url('fonts/lovely_font.ttf') format('truetype');
     font-weight:bold;
    }
    div { font-family: 'lovelyFont', sans-serif; }

# 总结

英文字体比较小，这种技术还是挺有用的。相比英文几K的字体，中文基本都是2M左右，实践还是有点困难。

来源：[http://www.webdesignerdepot.com/2013/01/how-to-use-any-font-you-like-with-css3/](http://www.webdesignerdepot.com/2013/01/how-to-use-any-font-you-like-with-css3/)