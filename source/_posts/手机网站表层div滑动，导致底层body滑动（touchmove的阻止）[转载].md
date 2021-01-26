---
title: "手机网站表层div滑动，导致底层body滑动（touchmove的阻止）[转载]"
categories: [ "JS" ]
tags: [ "js","touchmove" ]
draft: false
slug: "mobile-web-site-div-sliding-resulting-in-the-underlying-body-sliding-touchmove-block-reproduced"
date: "2015-10-24 21:07:00"
---

## 案例描述：
body很长，可以滑动，body头部有一个模拟下拉的选择框。下拉选择有滚动轴，如下图。
![yigrherb_slide.png][1]
我给body一个overflow：hidden和高度是没有用的。手机网站上背景还是可以滑动，然后我给body一个touchmove的preventdefault()阻止事件，body滑动阻止了，PC上面是可以了，但是手机上面滑动div还是会导致底部body的滑动，我给div 一个阻止冒泡的事件stopPropagation()，手机网站上面还是不可以。

关于preventdefault和stopPropagation，后面有时间会讲解其区别。
解决方案

我经过反复测试，发现滚动轴滚到底部的时候，会触发body的滑动，那么我就在事件滚到底部的时候对表层的div做一个touchmove的阻止。到达滚动轴底部，向下滑动，阻止事件，向上滑动，开启事件。为此就要判断touchmove的方向了。


<!--more-->


    $("body").on("touchstart", function(e) {
        e.preventDefault();
        startX = e.originalEvent.changedTouches[0].pageX,
        startY = e.originalEvent.changedTouches[0].pageY;
    });
    $("body").on("touchmove", function(e) {
        e.preventDefault();
        moveEndX = e.originalEvent.changedTouches[0].pageX,
        moveEndY = e.originalEvent.changedTouches[0].pageY,
        X = moveEndX - startX,
        Y = moveEndY - startY;
    
        if ( Math.abs(X) > Math.abs(Y) && X > 0 ) {
            alert("left 2 right");
        }
        else if ( Math.abs(X) > Math.abs(Y) && X < 0 ) {
            alert("right 2 left");
        }
        else if ( Math.abs(Y) > Math.abs(X) && Y > 0) {
            alert("top 2 bottom");
        }
        else if ( Math.abs(Y) > Math.abs(X) && Y < 0 ) {
            alert("bottom 2 top");
        }
        else{
            alert("just touch");
        }
    });

上面的方法是判断touchmove的滑动方向。

      $('#haorooms底层背景').bind("touchmove", function (e) {
            e.preventDefault();
        });
        $(".滚动的父亲").bind("touchstart", function (events) {
            startY = events.originalEvent.changedTouches[0].pageY;
        });
        $(".滚动的父亲 ul").bind("touchmove", function (e) {
            var ulheight = $(this).height();
            var scrollTop = $(this).scrollTop();
            var scrollheight = $(this)[0].scrollHeight;
            if (ulheight + scrollTop + 20 >= scrollheight) { //滚到底部20px左右
                $(".滚动的父亲").bind("touchmove", function (event) {
                    moveEndY = event.originalEvent.changedTouches[0].pageY,
                            theY = moveEndY - startY;
                    if (theY > 0) { //用上面的abs()更加准确！
                        $(".滚动的父亲").unbind("touchmove");
                    }
                    if (theY < 0) {
                        event.preventDefault();
                    }
                })
            }
            if (scrollTop < 20) {//滚到顶部20px左右
                $(".滚动的父亲").bind("touchmove", function (event) {
                    moveEndY = event.originalEvent.changedTouches[0].pageY,
                            theY = moveEndY - startY;
                    if (theY > 0) {
                        event.preventDefault();
                    }
                    if (theY < 0) {
                        $(".滚动的父亲").unbind("touchmove");
                    }
                })
            }
        });

以上方法基本上能够阻止body的滚动，但是，有时候还是会有问题，期待更好的解决方案！
来源：[http://www.haorooms.com/post/webapp_bodyslidebcdiv](http://www.haorooms.com/post/webapp_bodyslidebcdiv)


  [1]: https://imgs.gnux.cn/usr/uploads/2016/01/4204969176.png