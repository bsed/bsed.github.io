---
title: "IE6、IE7、IE8、FireFox css line-height兼容问题 "
categories: [ "Css" ]
tags: [ "line-height" ]
draft: false
slug: "css-line-height"
date: "2011-12-31 20:40:00"
---

针对firefox ie6 ie7 ie8的css样式中的line-height属性，以前我们大部分都是用!important来hack，对于ie6和firefox测试可以正常显示，但是ie7以上对!important可以正确解释，会导致页面没按要求显示！这些细节却往往影响了整个页面样式的美观性。搜索了一下，找到了一个针对IE7、IE8不错的hack方式，IE7使用*+html或*line-height: 方式。大概的总结如下：


<!--more-->

CSS可以参照如下定义，注意顺序:

    #menu { line-height: 23px; }  
    #menu { line-height: 26px\9; }
    #menu { *line-height: 23px; }
    #menu { _line-height: 23px; } 或者写成一句
    
    #menu { line-height:23px; line-height: 26px\9; *line-height: 23px; _line-height:23px; } 或者
    
    * html #menu { line-height: 23px; }
    *+html #menu { line-height: 23px; }