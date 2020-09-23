---
title: 纯css 实现footer sticker
date: 2014-08-28 23:01:00
updated: 2015-08-20 10:54:55
tags: 
- jquery
- js
- beauty
- cool-kitten
- scrilld-js
categories: 
- js

---
让footer一直在页面底部
**css:**


<!--more-->


    html, body, #sticker {height: 100%;}
    body > #sticker {height: auto; min-height: 100%;}
    #stickerCon {padding-bottom: 40px;} 
    #footer {margin-top:-40px; height: 40px; width: 100%; text-align: center; line-height: 40px; color: #ABA498; font-size: 12px;
    background: #fafafa; border-top:1px solid #E7E7E7;}

   

**html:**

    <div id="sticker">
        <div id="stickerCon"></div>
    </div>
    <div id="footer">footer</div>