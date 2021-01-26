---
title: "CSS firefox中设置图片灰度方法 "
categories: [ "JS" ]
tags: [ "css","firefox","grayscale" ]
draft: false
slug: "firefox-css-set-up-the-image-gray-method"
date: "2014-08-07 22:00:00"
---

## HTML中设置

    <svg xmlns="http://www.w3.org/2000/svg" id="img3" viewBox="0 0 66 84">
    <filter id="grayscale">
    <feColorMatrix type="matrix" values="0.2126 0.7152 0.0722 0 0
                       0.2126 0.7152 0.0722 0 0
                       0.2126 0.7152 0.0722 0 0
                       0 0 0 1 0"></feColorMatrix>
    </filter>
    <image xlink:href="http://www.baidu.com/img/bd_logo.png" filter="url(#grayscale)" width="66px" height="84px"></image>
    </svg>


<!--more-->


## CSS设置

    {
      -moz-filter: grayscale(100%);
      -webkit-filter: grayscale(100%);
      -o-filter: grayscale(100%);
      -ms-filter: grayscale(100%);
      filter: grayscale(100%);
    }

这个filter 方法在firefox 里面没有效果 即使加上-moz 也没有效果developer.mozilla.org
让网页变灰色

## 兼容各种浏览器

    html{filter: grayscale(100%);
    -webkit-filter: grayscale(100%);
    -moz-filter: grayscale(100%);
    -ms-filter: grayscale(100%);
    -o-filter: grayscale(100%); 
    filter: url("data:image/svg+xml;utf8,<svg xmlns=\'http://www.w3.org/2000/svg\'><filter id=\'grayscale\'><feColorMatrix type=\'matrix\' values=\'0.3333 0.3333 0.3333 0 0 0.3333 0.3333 0.3333 0 0 0.3333 0.3333 0.3333 0 0 0 0 0 1 0\'/></filter></svg>#grayscale");
    filter:progid:DXImageTransform.Microsoft.BasicImage(grayscale=1);
    -webkit-filter: grayscale(1);}