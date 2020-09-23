---
title: iPhone6和iPhone6 Plus CSS3 媒体查询（Media Queries）
date: 2014-08-04 09:33:00
updated: 2016-02-28 11:58:01
tags: 
- css3
- font-face
categories: 
- css

---
```css
    @media only screen and (min-device-width: 375px) and (max-device-width: 667px) and (orientation : portrait) {
         /*iPhone 6 Portrait*/
    }
    
    @media only screen and (min-device-width: 375px) and (max-device-width: 667px) and (orientation : landscape) {
         /*iPhone 6 landscape*/
    }
    


<!--more-->


    @media only screen and (min-device-width: 414px) and (max-device-width: 736px) and (orientation : portrait) {
         /*iPhone 6+ Portrait*/
    }
    
    @media only screen and (min-device-width: 414px) and (max-device-width: 736px) and (orientation : landscape) {
         /*iPhone 6+ landscape*/
    }
    
    @media only screen and (max-device-width: 640px), only screen and (max-device-width: 667px), only screen and (max-width: 480px){
         /*iPhone 6 and iPhone 6+ portrait and landscape*/
    }
    
    @media only screen and (max-device-width: 640px), only screen and (max-device-width: 667px), only screen and (max-width: 480px) and (orientation : portrait){
         /*iPhone 6 and iPhone 6+ portrait*/
    }
    
    @media only screen and (max-device-width: 640px), only screen and (max-device-width: 667px), only screen and (max-width: 480px) and (orientation : landscape){
         /*iPhone 6 and iPhone 6+ landscape*/
```

## 标准设备的 Media Queries
```css
/* 智能手机(纵向和横向) ----------- */
@media only screen
and (min-device-width : 320px)
and (max-device-width : 480px) {
/* Styles */
}

/* 智能手机 (横向) ----------- */
@media only screen
and (min-width : 321px) {
/* Styles */
}

/* 智能手机(纵向) ----------- */
@media only screen
and (max-width : 320px) {
/* Styles */
}

/* iPads (纵向和横向) ----------- */
@media only screen
and (min-device-width : 768px)
and (max-device-width : 1024px) {
/* Styles */
}

/* iPads (横向) ----------- */
@media only screen
and (min-device-width : 768px)
and (max-device-width : 1024px)
and (orientation : landscape) {
/* Styles */
}

/* iPads (纵向) ----------- */
@media only screen
and (min-device-width : 768px)
and (max-device-width : 1024px)
and (orientation : portrait) {
/* Styles */
}

/* 桌面和笔记本电脑 ----------- */
@media only screen
and (min-width : 1224px) {
/* Styles */
}

/* 大屏幕 ----------- */
@media only screen
and (min-width : 1824px) {
/* Styles */
}

/* iPhone 4 ----------- */
@media
only screen and (-webkit-min-device-pixel-ratio : 1.5),
only screen and (min-device-pixel-ratio : 1.5) {
/* Styles */
}
```
via: [css-tricks](http://css-tricks.com/snippets/css/media-queries-for-standard-devices/)
—
更新：响应设计需要一个 meta 标签，否则视窗不会用设备的宽度

<meta name="viewport" content="width=device-width, initial-scale=1">

详细资料可以查看 [http://kayosite.com/css3-media-query.html](http://kayosite.com/css3-media-query.html)