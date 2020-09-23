---
title: css3 box-sizing属性
date: 2014-08-14 09:48:00
updated: 2015-08-14 09:50:36
tags: 
- golang
- channel
categories: 
- go

---
### 语法：

    box-sizing: content-box|border-box|inherit;

### 定义：

box-sizing 属性允许您以特定的方式定义匹配某个区域的特定元素。
例如，假如您需要并排放置两个带边框的框，可通过将 box-sizing 设置为 "border-box"。这可令浏览器呈现出带有指定宽度和高度的框，并把边框和内边距放入框中。

也就是说以前我们如果用到了border和padding时，我们元素的width要相应的减掉，border和padding的宽度才能够保持宽度一致。但是用了box-sizing后，就可以不用减了。


<!--more-->


### 实例：

    <!DOCTYPE html>
    <html>
    <head>
    <style> 
    div.container
    {
    width:30em;
    border:1em solid;
    }
    div.box
    {
    box-sizing:border-box;
    -moz-box-sizing:border-box; /* Firefox */
    -webkit-box-sizing:border-box; /* Safari */
    width:50%;
    border:1em solid red;
    float:left;
    }
    </style>
    </head>
    <body>
    
    <div class="container">
    <div class="box">这个 div 占据左半部分。</div>
    <div class="box">这个 div 占据右半部分。</div>
    </div>
    
    </body>
    </html>