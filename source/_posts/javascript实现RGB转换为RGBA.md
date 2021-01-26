---
title: "javascript实现RGB转换为RGBA"
categories: [ "JS" ]
tags: [ "rgba","rgb" ]
draft: false
slug: "javascript-to-achieve-rgba-conversion-to-rgb"
date: "2013-10-17 18:38:00"
---

一个16进制表示RGB色，我想将它转换为RGBA表示。比如说：

    rgb( 128, 128, 255 ) --> rgba(   0,   0, 255,  .5 )
    rgb( 152, 177, 202 ) --> rgba(  50, 100, 150,  .5 ) // can be better(lower alpha)

有谁能告诉怎么实现么？能有算法那是最好了，谢谢！

基于陈士安桥答案，写的代码：

    function RGBtoRGBA(r, g, b){
        if((g==void 0) && (typeof r == 'string')){
            r = r.replace(/^\s*#|\s*$/g, '');
            if(r.length == 3){
                r = r.replace(/(.)/g, '$1$1');
            }
            g = parseInt(r.substr(2, 2), 16);
            b = parseInt(r.substr(4, 2), 16);
            r = parseInt(r.substr(0, 2), 16);
        }
   


<!--more-->


 
        var min, a = ( 255 - (min = Math.min(r, g, b)) ) / 255;
    
        return {
            r    : r = 0|( r - min ) / a,
            g    : g = 0|( g - min ) / a,
            b    : b = 0|( b - min ) / a,
            a    : a = (0|1000*a)/1000,
            rgba : 'rgba(' + r + ', ' + g + ', ' + b + ', ' + a + ')'
        };
    }
    
    RGBtoRGBA(204, 153, 102) == RGBtoRGBA('#CC9966') == RGBtoRGBA('C96') == 
        {
           r    : 170,
           g    : 85 ,
           b    : 0  ,
           a    : 0.6,
           rgba : 'rgba(170, 85, 0, 0.6)' 
        }


取最小的色彩分量然后将其转换为一个alpha值，例如：

    152 转换为alpha值 (255 - 152) / 255 ~ 0.404

然后将色彩分量减去最小的色彩分量值，然后除以alpha值：

    152 -> (152 - 152) / 0.404 = 0
    177 -> (177 - 152) / 0.404 ~ 62
    202 -> (202 - 152) / 0.404 ~ 123

我们就可以得到`rgb(152, 177, 202)` 转换为 `rgba(0, 62, 123, .404)`。
