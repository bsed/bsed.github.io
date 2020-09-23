---
title: 使用jQuery改变CSS属性一例问题
date: 2013-11-02 09:53:00
updated: 2015-07-05 13:01:55
tags: 
- border-radius
categories: 
- java

---
下午修改wordpress导航条（二级菜单）时遇到一个让人郁闷的问题，使用JQuery提供的css()方法来修改CSS属性。主要症状是：鼠标移到文字上时（hover）能正常显示CSS3的box-shadow，但是移出之后依然保留着hover状态……非常之蛋疼。


<!--more-->


先看我原先的代码。

    $(document).ready(function() {
     $("#navmenu ul li:has(ul)").hover(function() {
     $(this).children("a").css({
     'color':'#EEBC53','box-shadow':'rgba(0,0,0,0.5) 0px 2px 5px, inset rgba(255,255,255,0.25) 0px 1px 0px, inset rgba(0,0,0,0.25) 0px 0px 0px, inset rgba(255,255,255,0.03) 0px 20px 0px, inset rgba(0,0,0,0.15) 0px -20px 20px, inset rgba(255,255,255,0.05) 0px 20px 20px'
     });//使用了css()修改a:hover属性
     if ($(this).find("li").length > 0) {
     $(this).children("ul").stop(true, true).slideDown(400)
     }
     },
     function() {
     $(this).children("a").css({
     'color':'#ffffff'
     });//恢复a正常属性
     $(this).children("ul").stop(true, true).slideUp("fast")
     });
    })

从理论上来说，这段代码没有问题，调试不会出错，就是有点繁琐（先不扯这个）。

可是实际使用时就BUG了，出现了一开始那样的问题，无法正常恢复。查阅了很多资料，我也没找到问题所在，后来就瞎琢磨，无意间的一个灵光闪现居然解决了这个问题。

问题出在了从hover恢复过来时要去掉多余的属性，css()只是提供了添加和修改这两种功能，并没有“先重置后修改”的功能。也就是说，使用css()改变CSS属性时只是在原有的基础上进行变更。

比如原先属性如下，如果要使用css()去掉border属性，则需要这样写

`#test{color:#FFF;border:1px #000 solid;}`
通过css()给border设置一个空属性，浏览器就会识别为不包含border属性

`$("#test").css("'color':'#FFF','border':''")`
回到我一开始贴的那一段，只需要把最后恢复属性的一句写成如上形式，则可以去掉box-shadow属性。

唉，真的好蛋疼~