---
title: 纯CSS3加载Loader伪类旋转动画
date: 2015-08-04 13:07:00
updated: 2015-08-04 13:30:29
tags: 
- css3
categories: 
- css

---
今天给大家分享一个转圈的Loading动画，源代码是从别的网站上拔下来的，加载效果非常的好看，可以加在您的项目中，3个不同颜色的四分之一圆圈围绕中心旋转，旋转的速度不一样造成一种很炫的效果。
![loading-yuan.jpg][1]

**HTML代码**

    <div id="loader-wrapper">
    <div id="loader"></div>
    <div class="loader-txt">75%</div>
    </div>


<!--more-->


**CSS代码**

    .loader-txt{
        position: relative;
        top:50%;
        left:50%;
        margin: -75px 0 0 -75px;
        font-family:arial;
        width:150px;
        text-align:center;
    }
    #loader-wrapper {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        z-index: 1000;
    }
    #loader {
        display: block;
        position: relative;
        left: 50%;
        top: 50%;
        width: 150px;
        height: 150px;
        margin: -75px 0 0 -75px;
        border-radius: 50%;
        border: 3px solid transparent;
        border-top-color: #3498db;
    
        -webkit-animation: spin 2s linear infinite; /* Chrome, Opera 15+, Safari 5+ */
        animation: spin 2s linear infinite; /* Chrome, Firefox 16+, IE 10+, Opera */
        z-index: 1001;
    }
    
    #loader:before {
        content: "";
        position: absolute;
        top: 5px;
        left: 5px;
        right: 5px;
        bottom: 5px;
        border-radius: 50%;
        border: 3px solid transparent;
        border-top-color: #e74c3c;
    
        -webkit-animation: spin 3s linear infinite; /* Chrome, Opera 15+, Safari 5+ */
        animation: spin 3s linear infinite; /* Chrome, Firefox 16+, IE 10+, Opera */
    }
    
    #loader:after {
        content: "";
        position: absolute;
        top: 15px;
        left: 15px;
        right: 15px;
        bottom: 15px;
        border-radius: 50%;
        border: 3px solid transparent;
        border-top-color: #f9c922;
    
        -webkit-animation: spin 1.5s linear infinite; /* Chrome, Opera 15+, Safari 5+ */
          animation: spin 1.5s linear infinite; /* Chrome, Firefox 16+, IE 10+, Opera */
    }
    
    @-webkit-keyframes spin {
        0%   { 
            -webkit-transform: rotate(0deg);  /* Chrome, Opera 15+, Safari 3.1+ */
            -ms-transform: rotate(0deg);  /* IE 9 */
            transform: rotate(0deg);  /* Firefox 16+, IE 10+, Opera */
        }
        100% {
            -webkit-transform: rotate(360deg);  /* Chrome, Opera 15+, Safari 3.1+ */
            -ms-transform: rotate(360deg);  /* IE 9 */
            transform: rotate(360deg);  /* Firefox 16+, IE 10+, Opera */
        }
    }
    @keyframes spin {
        0%   { 
            -webkit-transform: rotate(0deg);  /* Chrome, Opera 15+, Safari 3.1+ */
            -ms-transform: rotate(0deg);  /* IE 9 */
            transform: rotate(0deg);  /* Firefox 16+, IE 10+, Opera */
        }
        100% {
            -webkit-transform: rotate(360deg);  /* Chrome, Opera 15+, Safari 3.1+ */
            -ms-transform: rotate(360deg);  /* IE 9 */
            transform: rotate(360deg);  /* Firefox 16+, IE 10+, Opera */
        }
    }
    
    
    .loaded #loader {
        opacity: 0;
    
        -webkit-transition: all 0.3s ease-out;  /* Android 2.1+, Chrome 1-25, iOS 3.2-6.1, Safari 3.2-6  */
                transition: all 0.3s ease-out;  /* Chrome 26, Firefox 16+, iOS 7+, IE 10+, Opera, Safari 6.1+  */
    
    }
    .loaded #loader-wrapper {
        visibility: hidden;
    
        -webkit-transform: translateY(-100%);  /* Chrome, Opera 15+, Safari 3.1+ */
            -ms-transform: translateY(-100%);  /* IE 9 */
                transform: translateY(-100%);  /* Firefox 16+, IE 10+, Opera */
    
        -webkit-transition: all 0.3s 1s ease-out;  /* Android 2.1+, Chrome 1-25, iOS 3.2-6.1, Safari 3.2-6  */
                transition: all 0.3s 1s ease-out;  /* Chrome 26, Firefox 16+, iOS 7+, IE 10+, Opera, Safari 6.1+  */
    }
  效果见：[demo](http://runjs.cn/code/b61ne0fo)
  [1]: https://imgs.gnux.cn/usr/uploads/2015/08/2897553559.jpg
