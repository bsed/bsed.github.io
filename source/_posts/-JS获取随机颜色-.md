---
title: " JS获取随机颜色 "
categories: [ "JS" ]
tags: [ "random","sjs" ]
draft: false
slug: "js-random-color"
date: "2011-11-01 20:45:00"
---

随机颜色生成

    function getRandomColor(){
      return "#"+("00000"+((Math.random()*16777215+0.5)>>0).toString(16)).slice(-6);
    }

slice

slice() 方法可从已有的数组中返回选定的元素。


<!--more-->


    arrayObject.slice(start,end)

| 数 | 描述 |
| --- | --- |
| star 	| 必需。规定从何处开始选取。如果是负数，那么它规定从数组尾部开始算起的位置。也就是说，-1 指最后一个元素，-2 指倒数第二个元素，以此类推。| 
| end 	| 可选。规定从何处结束选取。该参数是数组片断结束处的数组下标。如果没有指定该参数，那么切分的数组包含从 start 到数组结束的所有元素。如果这个参数是负数，那么它规定的是从数组尾部开始算起的元素。| 
