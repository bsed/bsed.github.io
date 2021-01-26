---
title: "$(this.el).html 与 this.$el.tml区别"
categories: [ "JS" ]
tags: [ "jquery","js","zepto" ]
draft: false
slug: "thiselhtml-and-thiseltml"
date: "2015-09-15 19:15:00"
---

 包装元素与jQuery（或的Zepto）。所以，如果你的视图的HTML是这样的：

    <div id="myViewElement"></div>

 

this.el引用该div 而$(this.el)将通过jq检索他等价的元素 $('#myViewElement').

this.el是一个缓存引用了jq或者zepto的对象，所有你会调用$(this.el).可以建设好开销。


<!--more-->


他两者是不等价的。单独this.el是参考一个宿主对象的HTMLElement - 没有图书馆参与。这是的document.getElementById的回报。 $（this.el）创建的jQuery/的Zepto对象的一个​​新实例。这个$ EL引用前者对象的单个实例。这不是“错”来使用其中任何一个，只要你理解的多次调用$（this.el）的费用。

看下面代码：

    this.ele = document.getElementById('myViewElement');
    this.$ele = $('#myViewElement');
    
    $('#myViewElement') == $(this.ele);

 

此外，值得一提的是，jQuery和的Zepto有部分内部缓存，所以额外调用$（this.el）可能最终反正返回一个缓存的结果，这就是为什么我说“可能有性能问题”。它也可能不会。
原文：[http://www.78oa.com/news/news882.html](http://www.78oa.com/news/news882.html)