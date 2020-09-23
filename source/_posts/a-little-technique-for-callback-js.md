---
title: Jquery 实现callback的一个小技巧
date: 2012-01-17 21:17:00
updated: 2015-08-12 21:23:37
tags: 
- golang
- docker
categories: 
- go

---
> 很多情况下，我们需要跨域读取数据，或者是调用别人json格式的api，都要用到js
> callback这种机制。通常做法是页面上定义一个A方法，再调用第三方的url并且把回调函数名A传过去。这样做固然没什么问题，但有没有更好的方法呢？

用过jQuery的同学肯定都知道，jQuery有一个`getJSON`的方法，只需要两个参数（callback地址和匿名函数）就能正常工作。摘录官方示例如下：

    $.getJSON("http://api.flickr.com/services/feeds/photos_public.gne?tags=cat&tagmode=any&format=json&jsoncallback=?",
            function(data){
            $.each(data.items, function(i,item){
                $("<img/>").attr("src", item.media.m).appendTo("#images");
                if ( i == 3 ) return false;
            });
    });


<!--more-->


去看下`jQuery`的源代码，你会发现其实原理很简单。如果懒得看太多代码，看看下面原生JS
你就知道　getJson是咋么实现的，

    function loadJSON(url,callback){
        var cn = "jscallback"+(+new Date());
        var s = document.createElement("script");
        s.type = "text/javascript";
        s.src = url+cn;
        window[cn] = callback;
        document.getElementsByTagName("head")[0].appendChild(s);
    };
    loadJSON("http://api.flickr.com/services/feeds/photos_public.gne?tags=cat&tagmode=any&format=json&jsoncallback=",function(o){
        for(var i=0;i<3;i++){
            var img = document.createElement("img");
            img.src = o.items[i].media.m;
            document.body.appendChild(img);
        }
    });


