---
title: "img设置CSS属性使图片居中"
categories: [ "Css" ]
tags: [ "css","img","图片居中" ]
draft: false
slug: "img-set-css-properties-img-align-center"
date: "2014-01-06 11:45:00"
---

CSS设置内容居中，可以用下面的代码实现

    text-align:center
但是这么做有一个缺点，只能在img标签的上级标签设置。能不能直接对img标签做一个设置是图片居中呢？


<!--more-->


当然可以，代码如下：

    .aligncenter {
     clear: both;
     display: block;
     margin:auto;
    }
这是wordpress的官方写法，只要把img的class设置为aligncenter 即可。同样的道理，居左和居右的代码

    .alignleft {
     display: inline;
     float: left;
    }
    .alignright {
     display: inline;
     float: right;
    }

参考：[这里](http://www.tantengvip.com/2011/10/css_img/)