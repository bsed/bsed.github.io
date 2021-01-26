---
title: "Web下的CSS分辨率设置"
categories: [ "Css" ]
tags: [ "css","web","media" ]
draft: false
slug: "css-resolution-settings-under-web"
date: "2012-08-04 11:16:00"
---

# 普通屏下图片

    /* for low resolution display */
    .image {
    background-image: url(/path/to/my/bak.png);
    background-size: 200px 300px;
    height: 300px;
    width: 200px;
   }


<!--more-->


# 屏幕小于768px

    @media screen and (max-width: 767px) {
    /*手机尺寸下的兼容样式*/
    }

# 横屏模式

    @media screen and (orientation:landscape){
    /*横屏模式下的兼容样式*/
    }

# 手机高清屏兼容

    @media only screen and (min-device-width:320px) and (max-device-width:568px) and (-webkit-min-device-pixel-ratio: 2) {
    /*高清手机尺寸下的兼容样式  主要是针对iphone4以上的高清屏幕图片发虚的解决写法。*/
    }

# 高清屏下图片兼容火狐、opera、谷歌

    /* for high resolution display */
    @media only screen and (min--moz-device-pixel-ratio: 2),
    only screen and (-o-min-device-pixel-ratio: 2/1),
    only screen and (-webkit-min-device-pixel-ratio: 2),
    only screen and (min-device-pixel-ratio: 2) {
    .image {
    background: url(/path/to/my/bak.png) no-repeat;
    background-size: 200px 400px;
    /* rest of your styles... */
    }
    }