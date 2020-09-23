---
title: jQuery动态加载部分脚本的技巧
date: 2012-11-12 16:30:00
updated: 2015-07-27 16:36:46
tags: 
- accept
categories: 
- css

---
![wKiAiVE8I8f1MTnqAAAaSKvsj1Y684.jpg][1]
如果你使用的是`jQuery`，它里面有一个内置的方法可以用来加载单个JS文件。当你需要延迟加载一些js插件或其它类型的文件时，可以使用这个方法。

# 一、jQuery 通过getScript()方法加载javascript

jQuery内置了一个方法可以加载单一的js文件；当加载完成后你可以在回调函数里执行后续操作。最基本的使用jQuery.getScript的方法是这样：


<!--more-->


    jQuery.getScript("/path/to/myscript.js", function(data, status, jqxhr) {/*
    做一些加载完成后需要执行的事情
    */});

这个getScript方法返回一个jqxhr，你可以像下面这样用它：

    jQuery.getScript("/path/to/myscript.js")
    .done(function() {
    /* content*/
    })
    .fail(function() {
    /* content*/
    });

最常见的使用jQuery.getScript的地方是延迟加载一个js插件，而且在加载完成时执行它：

     jQuery.getScript("jquery.cookie.js")
          .done(function() {
              jQuery.cookie("cookie_name", "value", {
                  expires: 7
              });
          });

# 二、缓存问题

有一个非常重要的问题，使用jQuery.getScript时，你需要用一个时间戳字符串跟在需要加载的js地址后面，防止它被缓存。但是，如果你希望这个脚本被缓存，你需要设置全局缓存变量，像下面这样：

    jQuery.ajaxSetup({
        cache: true
        });
    jQuery.ajax({
         url: "jquery.cookie.js",
         dataType: "script",
         cache: true
     }).done(function() {
         jQuery.cookie("cookie_name", "value", {
             expires: 7
         });
     });


在加载脚本时一定要小心缓存问题！


  [1]: https://imgs.gnux.cn/usr/uploads/2015/07/2690254774.jpg