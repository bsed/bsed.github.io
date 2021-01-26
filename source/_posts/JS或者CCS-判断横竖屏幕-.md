---
title: "JS或者CCS-判断横竖屏幕 "
categories: [ "JS" ]
tags: [ "js","css" ]
draft: false
slug: "js-or-ccs-to-determine-horizontal-screen"
date: "2013-11-19 20:49:00"
---

## orientation & orientationchange

用触发手机的横屏和竖屏之间的切换的事件
`window.orientation`:这个属性给出了当前设备的屏幕方向，0表示竖屏，正负90表示横屏（向左与向右）模式
`onorientationchange` : 在每次屏幕方向在横竖屏间切换后，就会触发这个window事件，用法与传统的事件类似

    window.addEventListener("orientationchange", function() {
    // 宣布新方向的数值
    alert(window.orientation);
    }, false);

使用`onorientationchange`事件的回调函数，来动态地为 `body` 标签添加一个叫 `orient` 的属性，同时以`body[orient=landspace]`或`body[orient=portrait]`的方式在css中定义对应的样式，这样就可以实现在不同的屏幕模式下显示不同的样式。

    <body orient="landspace"></body>


<!--more-->


## resize

监听调整大小的改变

    window.addEventListener("resize", function() {
    // 得到屏幕尺寸 (内部/外部宽度，内部/外部高度)  
    }, false);

## CSS

css判断横竖屏幕

    /* portrait */
    @media screen and (orientation:portrait) {
    /* portrait-specific styles */
    }
    /* landscape */
    @media screen and (orientation:landscape) {
    /* landscape-specific styles */
    }

## matchMedia

本地 `window.matchMedia` 方法允许实时媒体查询。我们可以利用以上媒体查询找到我们是处于直立或水平视角：

    var mql = window.matchMedia("(orientation: portrait)");
    // 如果有匹配，则我们处于垂直视角
    if(mql.matches) {  
        // 直立方向
        alert("1")
    } else {  
        //水平方向
        alert("2")
    }  
    // 添加一个媒体查询改变监听者
    mql.addListener(function(m) {
        if(m.matches) {
            // 改变到直立方向
            document.getElementById("test").innerHTML="改变到直立方向";
        }else {
            document.getElementById("test").innerHTML="改变到水平方向";
            // 改变到水平方向
        }
    });

## 兼容不支持orientation

如果目标移动浏览器不支持media query，同时window.orientation也不存在，则我们需要采用另外一种方式来实现————使用定时器“伪实时”地对比当前窗口的高(window.innerHeight)与宽(window.innerWidth)之比，从而判定当前的横竖屏状态。如下代码所示：

    (function(){
        var updateOrientation=function(){
            var orientation=(window.innerWidth > window.innerHeight)? "landscape" : "portrait";
            document.body.parentNode.setAttribute("class",orientation);
        };
    
        var init=function(){
            updateOrientation();
            window.setInterval(updateOrientation,5000);
        };
        window.addEventListener("DOMContentLoaded",init,false);
    })();

## 合并 定时器和orientationchange 方法

    (function(){
        var supportOrientation=(typeof window.orientation == "number" && typeof window.onorientationchange == "object");
        var updateOrientation=function(){
            if(supportOrientation){
                updateOrientation=function(){
                    var orientation=window.orientation;
                    switch(orientation){
                        case 90:
                        case -90:
                            orientation="landscape";
                            break;
                        default:
                            orientation="portrait";
                    }
                    document.body.parentNode.setAttribute("class",orientation);
                };
            }else{
                updateOrientation=function(){
                    var orientation=(window.innerWidth > window.innerHeight)? "landscape":"portrait";
                    document.body.parentNode.setAttribute("class",orientation);
                };
            }
            updateOrientation();
        };
    
        var init=function(){
            updateOrientation();
            if(supportOrientation){
                window.addEventListener("orientationchange",updateOrientation,false);
            }else{    
                window.setInterval(updateOrientation,5000);
            }
        };
        window.addEventListener("DOMContentLoaded",init,false);
    })();

