---
title: "30段超实用CSS代码"
categories: [ "Css" ]
tags: [ "css" ]
draft: false
slug: "30-section-of-super-practical-css-code"
date: "2014-05-18 17:33:00"
---

摘要：国外UX设计师Jake Rocheleau收集整理了50段超实用的CSS代码，上周，研发频道编译了前20段，本文是对后30个CSS特效的编译。

## 1.花式连字符（&）

这个类应该在span元素里使用，并且里面包括&字符。它使用经典的serif字体和斜体来增强&符号。

    .amp {
    
        font-family: Baskerville, 'Goudy Old Style', Palatino, 'Book Antiqua', serif;
    
        font-style: italic;
    
        font-weight: normal;
    
    }


<!--more-->


## 2.段落首字符下沉

通常，这种效果会出现在印刷媒体上，如报纸或书籍。同样，如果网页布局合理，它也可以使用在Web页面上，它仅针对段落使用，但你也可以与单个元素绑定。

    p:first-letter{
    
        display: block;
    
        margin: 5px 0 0 5px;
    
        float: left;
    
        color: #ff3366;
    
        font-size: 5.4em;
    
        font-family: Georgia, Times New Roman, serif;
    
    }

## 3.内层CSS3盒阴影

盒阴影（box shadow）属性几乎可以运用在任何元素上，它们看起来都非常好看。下面这段代码主要聚焦内层阴影的设计。

    #mydiv {
    
        -moz-box-shadow: inset 2px 0 4px #000;
    
        -webkit-box-shadow: inset 2px 0 4px #000;
    
        box-shadow: inset 2px 0 4px #000;
    
    }

## 4.外层CSS3盒阴影

下面介绍一段外层阴影代码设计，注意，代码里的第三个参数表示模糊距离（blur distance），而第四个参数表示铺开的（spread）距离。关于这些值的设计，你可以前往 W3Schools学习。

    #mydiv {
    
        -webkit-box-shadow: 0 2px 2px -2px rgba(0, 0, 0, 0.52);
    
        -moz-box-shadow: 0 2px 2px -2px rgba(0, 0, 0, 0.52);
    
        box-shadow: 0 2px 2px -2px rgba(0, 0, 0, 0.52);
    
    }

## 5.三角形列表符号

该符号只能在CSS3里生成，在主流浏览器中，这是一项非常酷的技术。而其唯一的潜在问题是缺乏对后退方法的支持。

        ul {
        
            margin: 0.75em 0;
        
            padding: 0 1em;
        
            list-style: none;
        
        }
    
    li:before {
    
        content: "";
    
        border-color: transparent #111;
    
        border-style: solid;
    
        border-width: 0.35em 0 0.35em 0.45em;
    
        display: block;
    
        height: 0;
    
        width: 0;
    
        left: -1em;
    
        top: 0.9em;
    
        position: relative;
    
    }

## 6.居中对齐并设置固定宽度

    #page-wrap {
    
        width: 800px;
    
        margin: 0 auto;
    
    }

## 7.CSS3列文本

    #columns-3 {
    
        text-align: justify;
    
        -moz-column-count: 3;
    
        -moz-column-gap: 12px;
    
        -moz-column-rule: 1px solid #c4c8cc;
    
        -webkit-column-count: 3;
    
        -webkit-column-gap: 12px;
    
        -webkit-column-rule: 1px solid #c4c8cc;
    
    }

## 8.固定页脚

在网页里添加固定的页脚其实非常简单，并且也很实用。有些网站的页脚设计得很漂亮，可以给网站呈现出一个完美的结尾。

    #footer {
    
        position: fixed;
    
        left: 0px;
    
        bottom: 0px;
    
        height: 30px;
    
        width: 100%;
    
        background: #444;
    
    }
    
    /* IE 6 */
    
    * html #footer {
    
        position: absolute;
    
        top: expression((0-(footer.offsetHeight)+(document.documentElement.clientHeight ? document.documentElement.
    
    clientHeight : document.body.clientHeight)+(ignoreMe = document.documentElement.scrollTop ?
    
     document.documentElement.scrollTop : document.body.scrollTop))+'px');
    
    }

## 9.IE 6下修复PNG格式的透明度

在网站里使用透明的图像已成为一种很普遍的做法，其始于.gif图片格式，但现在也涉及到.png图片格式。而一些老版本的IE不支持透明度，下面这段代码会很好地解决这一问题。

    .bg {
    
        width:200px;
    
        height:100px;
    
        background: url(/folder/yourimage.png) no-repeat;
    
        _background:none;
    
        _filter:progid:DXImageTransform.Microsoft.AlphaImageLoader(src='/folder/yourimage.png',sizingMethod='crop');
    
    }
    
    /* 1px gif method */
    
    img, .png {
    
        position: relative;
    
        behavior: expression((this.runtimeStyle.behavior="none")&&(this.pngSet?this.pngSet=true:(this.nodeName ==
    
     "IMG" && this.src.toLowerCase().indexOf('.png')>-1?(this.runtimeStyle.backgroundImage = "none",
    
           this.runtimeStyle.filter = "progid:DXImageTransform.Microsoft.AlphaImageLoader
    
    (src='" + this.src + "', sizingMethod='image')",
    
           this.src = "images/transparent.gif"):(this.origBg = this.origBg? this.origBg :this.currentStyle.
    
    backgroundImage.toString().replace('url("','').replace('")',''),
    
           this.runtimeStyle.filter = "progid:DXImageTransform.Microsoft.AlphaImageLoader(src='" + this.origBg + "', sizingMethod='crop')",
    
           this.runtimeStyle.backgroundImage = "none")),this.pngSet=true));
    
    }

## 10.跨浏览器设置最小高度

有时开发者需要对HTML元素设置最小高度，然而，这个效果却无法兼容IE和老版本的Firefox，下面这段代码可以修复这个问题。

    #container {
    
        min-height: 550px;
    
        height: auto !important;
    
        height: 550px;
    
    }

## 11. CSS3发光输入框

下面的这段代码重写了浏览器的默认行为，可以让Chrome和Safari浏览器下普通的表单输入框产生发光效果。

    input[type=text], textarea {
    
        -webkit-transition: all 0.30s ease-in-out;
    
        -moz-transition: all 0.30s ease-in-out;
    
        -ms-transition: all 0.30s ease-in-out;
    
        -o-transition: all 0.30s ease-in-out;
    
        outline: none;
    
        padding: 3px 0px 3px 3px;
    
        margin: 5px 1px 3px 0px;
    
        border: 1px solid #ddd;
    
    }
    
    input[type=text]:focus, textarea:focus {
    
        box-shadow: 0 0 5px rgba(81, 203, 238, 1);
    
        padding: 3px 0px 3px 3px;
    
        margin: 5px 1px 3px 0px;
    
        border: 1px solid rgba(81, 203, 238, 1);
    
    }

## 12.基于文件类型来创建链接样式

下面这段代码通过使用CSS选择器和图像图标来实现各种类型的链接样式，可能会用到各种协议（HTTP、FTP、IRC,mailto），或者是文件本身的类型（mp3、avi、pdf）。

    /* external links */
    
    a[href^="http://"] {
    
        padding-right: 13px;
    
        background: url('external.gif') no-repeat center right;
    
    }
    
    /* emails */
    
    a[href^="mailto:"] {
    
        padding-right: 20px;
    
        background: url('email.png') no-repeat center right;
    
    }
    
    /* pdfs */
    
    a[href$=".pdf"] {
    
        padding-right: 18px;
    
        background: url('acrobat.png') no-repeat center right;
    
    }

## 13.pre标签封装代码

pre标签常用来对代码进行布局，可以解决因为行太多带来的滚动条问题。下面这段代码就使用pre来封装代码，让内容直接显示在页面中。

    pre {
    
        white-space: pre-wrap;      
    
    /* css-3 */
    
        white-space: -moz-pre-wrap; 
    
    /* Mozilla, since 1999 */
    
        white-space: -pre-wrap;     
    
    /* Opera 4-6 */
    
        white-space: -o-pre-wrap;   
    
    /* Opera 7 */
    
        word-wrap: break-word;      
    
    /* Internet Explorer 5.5+ */
    
    }

## 14.鼠标指向时变成手型

网页中有许多item在点击时，鼠标不会变成小手的形状。这套CSS选择器会强迫鼠标越过一些关键元素和其他对象一起来使用.pointer这个类。

    a[href], input[type='submit'], input[type='image'], label[for], select, button, .pointer {
    
        cursor: pointer;
    
    }

## 15.页面顶部阴影

简单地把下面这段代码拷贝到页面里，它会在body元素之前产生黑色的，渐渐变淡的阴影。这种效果通常用来给一个文本框或网页元素加阴影。

    body:before {
    
        content: "";
    
        position: fixed;
    
        top: -10px;
    
        left: 0;
    
        width: 100%;
    
        height: 10px;
    
        -webkit-box-shadow: 0px 0px 10px rgba(0,0,0,.8);
    
        -moz-box-shadow: 0px 0px 10px rgba(0,0,0,.8);
    
        box-shadow: 0px 0px 10px rgba(0,0,0,.8);
    
        z-index: 100;
    
    }

## 16.气泡引用效果

这种效果常出现在论坛、创建公告牌或者是文本引用上。你只需把下面这段代码拷贝到样式表文件即可。 这里（需翻墙）提供了许多关于语音泡泡的代码片段和使用技巧，欢迎去围观。

    .chat-bubble {
    
        background-color: #ededed;
    
        border: 2px solid #666;
    
        font-size: 35px;
    
        line-height: 1.3em;
    
        margin: 10px auto;
    
        padding: 10px;
    
        position: relative;
    
        text-align: center;
    
        width: 300px;
    
        -moz-border-radius: 20px;
    
        -webkit-border-radius: 20px;
    
        -moz-box-shadow: 0 0 5px #888;
    
        -webkit-box-shadow: 0 0 5px #888;
    
        font-family: 'Bangers', arial, serif;
    
    }
    
    .chat-bubble-arrow-border {
    
        border-color: #666 transparent transparent transparent;
    
        border-style: solid;
    
        border-width: 20px;
    
        height: 0;
    
        width: 0;
    
        position: absolute;
    
        bottom: -42px;
    
        left: 30px;
    
    }
    
    .chat-bubble-arrow {
    
        border-color: #ededed transparent transparent transparent;
    
        border-style: solid;
    
        border-width: 20px;
    
        height: 0;
    
        width: 0;
    
        position: absolute;
    
        bottom: -39px;
    
        left: 30px;
    
    }

## 17.指定标题样式

该模板提供了所有头元素的主要样式，从H1到H5。

    h1,h2,h3,h4,h5{
    
        color: #005a9c;
    
    }
    
    h1{
    
        font-size: 2.6em;
    
        line-height: 2.45em;
    
    }
    
    h2{
    
        font-size: 2.1em;
    
        line-height: 1.9em;
    
    }
    
    h3{
    
        font-size: 1.8em;
    
        line-height: 1.65em;
    
    }
    
    h4{
    
        font-size: 1.65em;
    
        line-height: 1.4em;
    
    }
    
    h5{
    
        font-size: 1.4em;
    
        line-height: 1.25em;
    
    }

## 18.利用CSS生成纹理图案背景

通过CSS来为背景图片添加噪点，从而形成漂亮的纹理图案。你可以使用 NoiseTextureGenerator生成器自定义噪点的纹理图案。

    body {
    
        background-image: url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADIAAAAyCAMAAAAp4       
    
      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
    
    /wuqcXY47QILbgAAAABJRU5ErkJggg==);
    
    
        background-color: #0094d0;
    
    }

## 19.List Ordering

你可能会遇到这种列表情况，需要把这个列表拆分成两个UL元素，你不妨看看下面这段代码。

    ol.chapters {
    
        list-style: none;
    
        margin-left: 0;
    
    }
    
     
    
    ol.chapters > li:before {
    
        content: counter(chapter) ". ";
    
        counter-increment: chapter;
    
        font-weight: bold;
    
        float: left;
    
        width: 40px;
    
    }
    
     
    
    ol.chapters li {
    
        clear: left;
    
    }
    
     
    
    ol.start {
    
        counter-reset: chapter;
    
    }
    
     
    
    ol.continue {
    
        counter-reset: chapter 11;
    
    }

## 20.为文本添加悬停提示框

把这段代码拷贝到样式表中，使用新的HTML5数据属性，通过使用data-tooltip你就可以给文本添加悬停提示框了。

    a {
    
        border-bottom:1px solid #bbb;
    
        color:#666;
    
        display:inline-block;
    
        position:relative;
    
        text-decoration:none;
    
    }
    
    a:hover,
    
    a:focus {
    
        color:#36c;
    
    }
    
    a:active {
    
        top:1px;
    
    }
    
      
    
    /* Tooltip styling */
    
    a[data-tooltip]:after {
    
        border-top: 8px solid #222;
    
        border-top: 8px solid hsla(0,0%,0%,.85);
    
        border-left: 8px solid transparent;
    
        border-right: 8px solid transparent;
    
        content: "";
    
        display: none;
    
        height: 0;
    
        width: 0;
    
        left: 25%;
    
        position: absolute;
    
    }
    
    a[data-tooltip]:before {
    
        background: #222;
    
        background: hsla(0,0%,0%,.85);
    
        color: #f6f6f6;
    
        content: attr(data-tooltip);
    
        display: none;
    
        font-family: sans-serif;
    
        font-size: 14px;
    
        height: 32px;
    
        left: 0;
    
        line-height: 32px;
    
        padding: 0 15px;
    
        position: absolute;
    
        text-shadow: 0 1px 1px hsla(0,0%,0%,1);
    
        white-space: nowrap;
    
        -webkit-border-radius: 5px;
    
        -moz-border-radius: 5px;
    
        -o-border-radius: 5px;
    
        border-radius: 5px;
    
    }
    
    a[data-tooltip]:hover:after {
    
        display: block;
    
        top: -9px;
    
    }
    
    a[data-tooltip]:hover:before {
    
        display: block;
    
        top: -41px;
    
    }
    
    a[data-tooltip]:active:after {
    
        top: -10px;
    
    }
    
    a[data-tooltip]:active:before {
    
        top: -42px;
    
    }

## 21.创建暗灰色的按钮样式

下面这段代码创建了CSS3按钮类，并根据按钮颜色命名为.graybtn。当然你也可以根据自己的网站风格对颜色进行更改。

    .graybtn {
    
        -moz-box-shadow:inset 0px 1px 0px 0px #ffffff;
    
        -webkit-box-shadow:inset 0px 1px 0px 0px #ffffff;
    
        box-shadow:inset 0px 1px 0px 0px #ffffff;
    
        background:-webkit-gradient( linear, left top, left bottom, color-stop(0.05, #ffffff), color-stop(1, #d1d1d1) );
    
        background:-moz-linear-gradient( center top, #ffffff 5%, #d1d1d1 100% );
    
        filter:progid:DXImageTransform.Microsoft.gradient(startColorstr='#ffffff', endColorstr='#d1d1d1');
    
        background-color:#ffffff;
    
        -moz-border-radius:6px;
    
        -webkit-border-radius:6px;
    
        border-radius:6px;
    
        border:1px solid #dcdcdc;
    
        display:inline-block;
    
        color:#777777;
    
        font-family:arial;
    
        font-size:15px;
    
        font-weight:bold;
    
        padding:6px 24px;
    
        text-decoration:none;
    
        text-shadow:1px 1px 0px #ffffff;
    
    }
    
    .graybtn:hover {
    
        background:-webkit-gradient( linear, left top, left bottom, color-stop(0.05, #d1d1d1), color-stop(1, #ffffff) );
    
        background:-moz-linear-gradient( center top, #d1d1d1 5%, #ffffff 100% );
    
        filter:progid:DXImageTransform.Microsoft.gradient(startColorstr='#d1d1d1', endColorstr='#ffffff');
    
        background-color:#d1d1d1;
    
    }
    
    .graybtn:active {
    
        position:relative;
    
        top:1px;
    
    }

## 22.打印的页面上显示URL

这条规则会使打印出来的页面在超链接文字后面加上URL，URL会被放在一组括号里面,前后各留一个空格。

    @media print   { 
    
      a:after { 
    
        content: " [" attr(href) "] "; 
    
      } 
    
    }

## 23.屏蔽Webkit移动浏览器中元素高亮效果

在访问移动网站时，你会发现，在选中的元素周围会出现一些灰色的框框，而下面这段代码即可屏蔽这种样式。

    body {
    
        -webkit-touch-callout: none;
    
        -webkit-user-select: none;
    
        -khtml-user-select: none;
    
        -moz-user-select: none;
    
        -ms-user-select: none;
    
        user-select: none;
    
    }

## 24.利用CSS生成小圆点图案

通过下面的代码，可以让你的网站背景上产生一些小圆点装饰图案。默认情况下，它是使用在body元素上，但你也可以把它使用在网页里的其他容器上。

    body {
    
        background: radial-gradient(circle, white 10%, transparent 10%),
    
        radial-gradient(circle, white 10%, black 10%) 50px 50px;
    
        background-size: 100px 100px;
    
    }

## 25.CSS3棋盘格效果

和上面的小圆点设计一样，这个效果需要一些额外的语法才可以工作，它需要在支持CSS3的浏览器上运行，效果很艳丽。当然，你可以根据需要自定义颜色。

    body {
    
        background-color: white;
    
        background-image: linear-gradient(45deg, black 25%, transparent 25%, transparent 75%, black 75%, black),
    
        linear-gradient(45deg, black 25%, transparent 25%, transparent 75%, black 75%, black);
    
        background-size: 100px 100px;
    
        background-position: 0 0, 50px 50px;
    
    }

## 26.Github Fork红丝带

下面这段代码是利用CSS3的transform属性生成Fork me on Github红丝带效果。

    .ribbon {
    
        background-color: #a00;
    
        overflow: hidden;
    
        
    
    /* top left corner */
    
        position: absolute;
    
        left: -3em;
    
        top: 2.5em;
    
        
    
    /* 45 deg ccw rotation */
    
        -moz-transform: rotate(-45deg);
    
        -webkit-transform: rotate(-45deg);
    
        
    
    /* shadow */
    
        -moz-box-shadow: 0 0 1em #888;
    
        -webkit-box-shadow: 0 0 1em #888;
    
    }
    
    .ribbon a {
    
        border: 1px solid #faa;
    
        color: #fff;
    
        display: block;
    
        font: bold 81.25% 'Helvetiva Neue', Helvetica, Arial, sans-serif;
    
        margin: 0.05em 0 0.075em 0;
    
        padding: 0.5em 3.5em;
    
        text-align: center;
    
        text-decoration: none;
    
        
    
    /* shadow */
    
        text-shadow: 0 0 0.5em #444;
    
    }

## 27.字体压缩

在样式表里使用如下代码能够帮你节省许多网页空间。

    p {
    
      font: italic small-caps bold 1.2em/1.0em Arial, Tahoma, Helvetica;
    
    }

## 28.纸页面卷曲效果

这种效果可以被广泛的使用在多种容器中，查看 demo page页面来更好地掌握该函数的使用吧。

    ul.box {
    
        position: relative;
    
        z-index: 1;
    
    /* prevent shadows falling behind containers with backgrounds */
    
        overflow: hidden;
    
        list-style: none;
    
        margin: 0;
    
        padding: 0;
    
    }
    
     
    
    ul.box li {
    
        position: relative;
    
        float: left;
    
        width: 250px;
    
        height: 150px;
    
        padding: 0;
    
        border: 1px solid #efefef;
    
        margin: 0 30px 30px 0;
    
        background: #fff;
    
        -webkit-box-shadow: 0 1px 4px rgba(0, 0, 0, 0.27), 0 0 40px rgba(0, 0, 0, 0.06) inset;
    
        -moz-box-shadow: 0 1px 4px rgba(0, 0, 0, 0.27), 0 0 40px rgba(0, 0, 0, 0.06) inset;
    
        box-shadow: 0 1px 4px rgba(0, 0, 0, 0.27), 0 0 40px rgba(0, 0, 0, 0.06) inset;
    
    }
    
     
    
    ul.box li:before,
    
    ul.box li:after {
    
        content: '';
    
        z-index: -1;
    
        position: absolute;
    
        left: 10px;
    
        bottom: 10px;
    
        width: 70%;
    
        max-width: 300px;
    
    /* avoid rotation causing ugly appearance at large container widths */
    
        max-height: 100px;
    
        height: 55%;
    
        -webkit-box-shadow: 0 8px 16px rgba(0, 0, 0, 0.3);
    
        -moz-box-shadow: 0 8px 16px rgba(0, 0, 0, 0.3);
    
        box-shadow: 0 8px 16px rgba(0, 0, 0, 0.3);
    
        -webkit-transform: skew(-15deg) rotate(-6deg);
    
        -moz-transform: skew(-15deg) rotate(-6deg);
    
        -ms-transform: skew(-15deg) rotate(-6deg);
    
        -o-transform: skew(-15deg) rotate(-6deg);
    
        transform: skew(-15deg) rotate(-6deg);
    
    }
    
     
    
    ul.box li:after {
    
        left: auto;
    
        right: 10px;
    
        -webkit-transform: skew(15deg) rotate(6deg);
    
        -moz-transform: skew(15deg) rotate(6deg);
    
        -ms-transform: skew(15deg) rotate(6deg);
    
        -o-transform: skew(15deg) rotate(6deg);
    
        transform: skew(15deg) rotate(6deg);
    
    }

## 29.发光的锚链接

下面这段代码可以创建自定义的锚链接，鼠标悬浮在上面会发光。

    a {
    
        color: #00e;
    
    }
    
    a:visited {
    
        color: #551a8b;
    
    }
    
    a:hover {
    
        color: #06e;
    
    }
    
    a:focus {
    
        outline: thin dotted;
    
    }
    
    a:hover, a:active {
    
        outline: 0;
    
    }
    
    a, a:visited, a:active {
    
        text-decoration: none;
    
        color: #fff;
    
        -webkit-transition: all .3s ease-in-out;
    
    }
    
    a:hover, .glow {
    
        color: #ff0;
    
        text-shadow: 0 0 10px #ff0;
    
    }

## 30.创建CSS3 Banner

在支持CSS3的浏览器中，下面的代码可以生成漂亮的纯CSS图案。这种效果常见于电子商务网站的产品图片、缩略图、视频预览等。

    .featureBanner {
    
        position: relative;
    
        margin: 20px
    
    }
    
    .featureBanner:before {
    
        content: "Featured";
    
        position: absolute;
    
        top: 5px;
    
        left: -8px;
    
        padding-right: 10px;
    
        color: #232323;
    
        font-weight: bold;
    
        height: 0px;
    
        border: 15px solid #ffa200;
    
        border-right-color: transparent;
    
        line-height: 0px;
    
        box-shadow: -0px 5px 5px -5px #000;
    
        z-index: 1;
    
    }
    
    .featureBanner:after {
    
        content: "";
    
        position: absolute;
    
        top: 35px;
    
        left: -8px;
    
        border: 4px solid #89540c;
    
        border-left-color: transparent;
    
        border-bottom-color: transparent;
    
    }

