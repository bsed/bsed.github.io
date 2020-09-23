---
title: 甩掉css3各种浏览器前缀？
date: 2012-08-18 10:49:00
updated: 2015-08-09 10:52:10
tags: 
- golang
categories: 
- go

---
假如你现在正学习着强大的Css3，你知道Css3的很多数属性为实验属性，使用他们的时候得加上各式各样的浏览器前缀。可能你默默忍受了，因为还没到统一的时间。有没想过给自己减下负，偶然间在网上看到一个js解决方案，正好可以解决这个问题。他会帮你自动识别浏览器，生成对应的Css3样式前缀，这样你就可以直接当作标准属性来使用了。

举个例子：


<!--more-->


**原先**：

    -webkit-transform: rotate(15deg);
    
    -o-transform: rotate(15deg);
    
    -moz-transform: rotate(15deg);
    
    -ms-transform: rotate(15deg);
    
    -webkit-border-radius: 50%;
    
    -o-border-radius: 50%;
    
    -moz-border-radius: 50%;
    
    -ms-border-radius: 50%;

**现在**：

    transform: rotate(15deg);
    
    border-radius: 50%;

是不是很棒，使用方法也很简单，简单到和平常引用jQuery一样，不用任何配置，而且文件大小也仅有几K。

引用方式：
`<script src=”http://leaverou.github.com/prefixfree/prefixfree.min.js”></script>`

该js项目地址为http://leaverou.github.com/prefixfree/。事物总是有两面性，带来方便的同时，也引入了一些问题，因为是基于js的，所以对于一些不支持或者禁用js的浏览器用户是很不友好的，很可能会打乱布局，而且有时候会影响你使用Css的一些技巧（比如你只是想针对某个浏览器使用某个Css3属性，由于这个js的作用，所有浏览器都会应用该条属性，明显这不是你想要的结果）

总结：用于学习练手我很赞成（少敲了好多代码哈哈），用于实际项目的话还是得考虑清楚其中的得失利弊。