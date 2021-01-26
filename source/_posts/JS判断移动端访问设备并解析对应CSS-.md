---
title: "JS判断移动端访问设备并解析对应CSS "
categories: [ "JS" ]
tags: [ "js" ]
draft: false
slug: "js-to-determine-the-mobile-terminal-access-equipment-and-resolve-the-corresponding-css"
date: "2013-11-03 21:34:00"
---

    判断不同web访问环境，主要针对移动设备，提供相对应的解析方案（判断设备代码直接copy腾讯网的）
    
    // 判断是否为移动端运行环境
    // wukong.name 20130716
    if(/AppleWebKit.*Mobile/i.test(navigator.userAgent) || (/MIDP|SymbianOS|NOKIA|SAMSUNG|LG|NEC|TCL|Alcatel|BIRD|DBTEL|Dopod|PHILIPS|HAIER|LENOVO|MOT-|Nokia|SonyEricsson|SIE-|Amoi|ZTE/.test(navigator.userAgent))){
      if(window.location.href.indexOf("?mobile")<0){
        try{
          if(/Android|webOS|iPhone|iPod|BlackBerry/i.test(navigator.userAgent)){
            // 判断访问环境是 Android|webOS|iPhone|iPod|BlackBerry 则加载以下样式
            setActiveStyleSheet("style_mobile_a.css");
          }else if(/iPad/i.test(navigator.userAgent)){
            // 判断访问环境是 iPad 则加载以下样式
            setActiveStyleSheet("style_mobile_iPad.css");
          }else{
            // 判断访问环境是 其他移动设备 则加载以下样式
            setActiveStyleSheet("style_mobile_other.css");
          }


<!--more-->


        }catch(e){}
      }
    }else{
      // 如果以上都不是，则加载以下样式
      setActiveStyleSheet("style_mobile_no.css");
    }
    // 判断完毕后加载样式
    function setActiveStyleSheet(filename){
      document.write("<link href="+filename+" rel=stylesheet>");
    }