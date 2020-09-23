---
title: 纯CSS的工具提示效果
date: 2013-08-03 15:53:00
updated: 2015-08-03 15:54:19
tags: 
- zepto
- detect
categories: 
- js

---

`<a href="#">普通链接 <span>纯CSS实现Tooltip效果</span></a>`
 


<!--more-->


    <style type="text/css">
        body {
            margin:0;
            padding:40px 80px;
            background:#fff;
            font:70% Arial, Helvetica, sans-serif;
            color:#555;
            line-height:180%;
        }
        h1, h2{ font-size:180%; font-weight:normal; color:#555; }
        h2{ font-size:140%; }    
        p{ margin:1em 0; width:500px; }
        a{ color:#f20; text-decoration:none; z-index:10;}
        a:hover{ color:#999; position:relative; z-index:100; }       
     
        a span{ display:none; }
        a:hover span{
            display:block;
            position:absolute;
            float:left;
            white-space:nowrap;
            top:-2.2em;
            left:.5em;
            background:#fffcd1;
            border:1px solid #444;
            color:#444;
            padding:1px 5px;
            z-index:10;
        }
    </style>

