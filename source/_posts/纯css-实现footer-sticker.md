---
title: "纯css 实现footer sticker"
categories: [ "Css" ]
tags: [ "css","footer","sticjer" ]
draft: false
slug: "css-footer-sticker"
date: "2014-08-28 23:01:00"
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