---
title: 纯CSS3制作的loading动画效果
date: 2014-04-04 13:33:00
updated: 2015-08-04 13:37:24
tags: 
- html5
- slides
categories: 
- css

---
今天分享的这个CSS3制作的loading动画效果很有科技感，全部由竖线构成，不断变化线条的长度达到动画的效果，有点像DNA的分子链，很适合科技类和医疗类网站的loading加载。

# 先来看看效果
![20140804133628.png][1]
<!--more-->


**HTML代码**

    <div class="loader">
      <span></span>
      <span></span>
      <span></span>
      <span></span>
      <span></span>
      <span></span>
      <span></span>
      <span></span>
      <span></span>
      <span></span>
      <span></span>
      <span></span>
      <span></span>
      <span></span>
      <span></span>
    </div>

**CSS代码**

    * { margin: 0px; padding: 0px; border: 0px; }
    html, body { min-height: 100%; }
    body { background: radial-gradient(#eeeeee, #444444); }
    .loader { position: absolute; top: 0px; bottom: 0px; left: 0px; right: 0px; margin: auto; width: 175px; height: 100px; }
    .loader span { display: block; background: #ccc; width: 7px; height: 10%; border-radius: 14px; margin-right: 5px; float: left; margin-top: 25%; }
    .loader span:last-child { margin-right: 0px; }
    .loader span:nth-child(1) { animation: load 2.5s 1.4s infinite linear; }
    .loader span:nth-child(2) { animation: load 2.5s 1.2s infinite linear; }
    .loader span:nth-child(3) { animation: load 2.5s 1s infinite linear; }
    .loader span:nth-child(4) { animation: load 2.5s 0.8s infinite linear; }
    .loader span:nth-child(5) { animation: load 2.5s 0.6s infinite linear; }
    .loader span:nth-child(6) { animation: load 2.5s 0.4s infinite linear; }
    .loader span:nth-child(7) { animation: load 2.5s 0.2s infinite linear; }
    .loader span:nth-child(8) { animation: load 2.5s 0s infinite linear; }
    .loader span:nth-child(9) { animation: load 2.5s 0.2s infinite linear; }
    .loader span:nth-child(10) { animation: load 2.5s 0.4s infinite linear; }
    .loader span:nth-child(11) { animation: load 2.5s 0.6s infinite linear; }
    .loader span:nth-child(12) { animation: load 2.5s 0.8s infinite linear; }
    .loader span:nth-child(13) { animation: load 2.5s 1s infinite linear; }
    .loader span:nth-child(14) { animation: load 2.5s 1.2s infinite linear; }
    .loader span:nth-child(15) { animation: load 2.5s 1.4s infinite linear; }
    @keyframes load {
     0% { background: #ccc; margin-top: 25%; height: 10%;}
     50% { background: #444; height: 100%; margin-top: 0%;}
     100% { background: #ccc; height: 10%; margin-top: 25%;
    }
    }

项目地址：[http://codepen.io/MyXoToD/pen/Djnbq](http://codepen.io/MyXoToD/pen/Djnbq)


  [1]: https://imgs.gnux.cn/usr/uploads/2015/08/1148328407.png