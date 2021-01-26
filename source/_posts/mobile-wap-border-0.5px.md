---
title: "mobile wap border 0.5px"
categories: [ "Css" ]
tags: [ "css","border" ]
draft: false
slug: "wap-border-05px-mobile"
date: "2014-11-03 17:23:00"
---

    .background-t-1{  /*通过设置背景色的方式*/
        background-image: -webkit-linear-gradient(bottom,transparent 50%,#dcdcdc 50%);/*使用背景色注意一下这个颜色，统一是使用dcdcdc。如果需要用其他颜色和小顾老师确认下*/
        background-image: linear-gradient(to bottom,transparent 50%,#dcdcdc 50%);
        background-size:100% 1px;
        background-repeat: no-repeat;
        background-position: bottom;
    }
    .border-1{ /*左右边框*/
        border-width: 0 1px 0 1px;
        border-image: url(http://img1.40017.cn/touch/public/borderImage/border-dc-abeam.png) 0 2 stretch;
    }
     


<!--more-->


    .background-b-1{
        background-image: -webkit-linear-gradient(top,transparent 50%,#dcdcdc 50%);
        /*先透明和dcdcdc是因为不倒一下的话左右边框和这个边框会有.5px的间隙*/
        background-image: linear-gradient(to top,transparent 50%,#dcdcdc 50%);
        background-size:100% 1px;
        background-repeat: no-repeat;
        background-position: top;
    }
     
    .background-t-2{
        background-image: -webkit-linear-gradient(top,#dcdcdc 50%,transparent 50%);
        background-image: linear-gradient(to top,#dcdcdc 50%,transparent 50%);
        background-size:100% 1px;
        background-repeat: no-repeat;
        background-position: top;
        padding-top: 1px;
    }
    .border-2{ /*上下边框*/
        border-width: 1px 0 1px 0;
        border-image: url(http://img1.40017.cn/touch/public/borderImage/border-dc.png) 2 0 stretch;
    }
     
    /*全部通过背景实现*/
    /*
    <div class="bg-border"></div>
     
    */
    .bg-border{
        width: 15px;
        height: 15px;
        background-image: linear-gradient(to bottom,transparent 96.9%,#dcdcdc 3.1%),linear-gradient(to top,transparent 96.9%,#dcdcdc 3.1%),linear-gradient(to left,transparent 96.9%,#dcdcdc 3.1%),linear-gradient(to right,#f5f5f5 96.9%,#dcdcdc 3.1%);
    /*这里可以根据需求显示哪几个边，（这个方法有个缺陷，必须要定宽度 或者高度，根据实际情况决定，因为每一个边都是根据宽度或者高 计算比例得到0.5px）*/
     
    /*https路径*/
    /*
     
    https://security.40017.cn/touch/img/public/borderImage/border-dc.png
     
    https://security.40017.cn/touch/img/public/borderImage/border-dc-abeam.png
     
    */
    }