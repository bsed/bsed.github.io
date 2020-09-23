---
title: CSS：25个常用的CSS技巧[转]
date: 2013-08-04 11:25:00
updated: 2015-08-04 11:58:15
tags: 
- 浏览器
categories: 
- css

---
这是非常有用的一种方法，例如处理公司logo的时候。如果logo是一个图片，但因为SEO又需要把公司名称写在h1标签中。我们想到的最好的方法是：h1标签中将文字推离显示区域，并将logo当做背景显示出来。

    h1{
        text-indent:-9999px;
        margin:0 auto;
        width:400px;
        height:100px;
        background:transparent url("images/logo.jpg") no-repeat scroll;
    }

根据链接属性统一替换链接样式

这个小技巧是针对用户体验的一种改善，经常上网的我们发现偶尔点出来的链接是email、pdf将会打开相应的应用程序，这个本不是我们想做的。这个技巧就是将页面中所有的链接都有个标注,很明显的区分普通链接、email链接、pdf链接，实现方法就是在不同链接属性的前边加上一个icon。

    /* external links */
    a[href^="http://"]{
        padding-right: 20px;
        background: url(external.gif) no-repeat center right;
    }
    
    /* emails */
    a[href^="mailto:"]{
        padding-right: 20px;
        background: url(email.png) no-repeat center right;
    }
    
    /* pdfs */
    a[href$=".pdf"]{
        padding-right: 20px;
        background: url(pdf.png) no-repeat center right;
    }

# IE浏览器移除textarea的滚动条

IE有个问题就是无论文本是否溢出都会给textarea添加滚动条，使用这个技巧可以轻松解决这个问题。

    textarea{
        overflow:auto;
    }

# 首字母、文字下沉

博客和新闻站点经常会看到这种首字母下沉的效果。其实他的实现方法挺简单，而且对老浏览器有平滑兼容。

    p:first-letter{
        display:block;
        margin:5px 0 0 5px;
        float:left;
        color:#FF3366;
        font-size:60px;
        font-family:Georgia;
    }

# 兼容透明度

透明度在跨浏览器上的兼容写法也是一个挺让人头疼的问题。下边是一种透明度兼容多浏览器的写法。

    .transparent{
        filter:alpha(opacity=50);
        -moz-opacity:0.5;
        -khtml-opacity:0.5;
        opacity:0.5;
    }

# Css Reset标准写法

当你开始编写样式文件的时候，Eric Meyer的样式重置几乎成为了当前的标准写法。通过参考一些业内有名的前端工程师的写法，你可以保证样式重置的质量。

    html, body, div, span, applet, object, iframe,
    h1, h2, h3, h4, h5, h6, p, blockquote, cc,
    a, abbr, acronym, address, big, cite, code,
    del, dfn, em, font, img, ins, kbd, q, s, samp,
    small, strike, strong, sub, sup, tt, var,
    b, u, i, center,
    dl, dt, dd, ol, ul, li,
    fieldset, form, label, legend,
    table, caption, tbody, tfoot, thead, tr, th, td {
        margin: 0;
        padding: 0;
        border: 0;
        outline: 0;
        font-size: 100%;
        vertical-align: baseline;
        background: transparent;
    }
    
    body {
        line-height: 1;
    }
    
    ol, ul {
        list-style: none;
    }
    
    blockquote, q {
        quotes: none;
    }
    
    blockquote:before, blockquote:after,
    q:before, q:after {
        content: '';
        content: none;
    }
    
    /* remember to define focus styles! */
    :focus {
        outline: 0;
    }
    
    /* remember to highlight inserts somehow! */
    ins {
        text-decoration: none;
    }
    
    del {
        text-decoration: line-through;
    }
    
    /* tables still need 'cellspacing="0"' in the markup */
    table {
        border-collapse: collapse;
        border-spacing: 0;
    }

# 图片预加载

有些时候图片预加载是非常有用的，尤其当这个元素是必要的，如果图片提前加载后您将不会感到任何的延迟或者抖动。处理轮播广告的时候经常能用到。

    div.loader{
        background:url(images/hover.gif) no-repeat;
        background:url(images/hover2.gif) no-repeat;
        background:url(images/hover3.gif) no-repeat;
        margin-left:-10000px;
    }

# CSS Sprite 按钮

现在按钮或者链接使用背景图片已经是非常普遍了，如今我们需要进一步改善用户体验，一旦用户鼠标悬停在按钮上给出一个反馈。方法就是通过css sprite改变按钮背景图片的位置下降或者上升一定高度，切换它另一种状态。

    a {
    display: block;
    background: url(sprite.png) no-repeat;
    height: 30px;
    width: 250px;
    }
    
    a:hover {
        background-position: 0 -30px;
    }

# Google 字体API

最近Google针对网页设计师发布了字体API，让他们可以通过Google Font API加载不同的新字体。我们甚至可以加载不同类型的字体，如粗体、斜体等。虽然Google的字库数量比较有限，仍然丰富了我们的设计。更多参考http://www.google.com/fonts/
第一步：首先在页面中添加字体的样式连接


第二步：在需要使用字体的地方用font-family给元素定义样式：

    h3{font-family: 'Fontdiner Swanky', arial, serif; }

# 浏览器的特殊hacks

浏览器hack针对解决特定浏览器的不一致情况是非常有效的，可以针对有问题的那个浏览器单独定义样式。

    /* IE 6 */
    * html .yourclass { }
    
    /* IE 7 */
    *+html .yourclass{ }
    
    /* IE 7 and modern browsers */
    html>body .yourclass { }
    
    /* Modern browsers (not IE 7) */
    html>/**/body .yourclass { }
    
    /* Opera 9.27 and below */
    html:first-child .yourclass { }
    
    /* Safari */
    html[xmlns*=""] body:last-child .yourclass { }
    
    /* Safari 3+, Chrome 1+, Opera 9+, Fx 3.5+ */
    body:nth-of-type(1) .yourclass { }
    
    /* Safari 3+, Chrome 1+, Opera 9+, Fx 3.5+ */
    body:first-of-type .yourclass {  }
    
    /* Safari 3+, Chrome 1+ */
    @media screen and (-webkit-min-device-pixel-ratio:0) {
     .yourclass  {  }
    }

# Fixed绝对定位页底

你会以为创建一个绝对定位的页底效果非常困难，当你去做的时候其实挺简单。只需要给IE6一个Hack写法，其余的都是用fixed定位即可。

    #footer {
        position:fixed;
        left:0px;
        bottom:0px;
        height:30px;
        width:100%;
        background:#999;
    }
    
    /* IE 6 */
    * html #footer {
        position:absolute;
        top:exccssion((0-(footer.offsetHeight)+(document.documentElement.clientHeight ? document.documentElement.clientHeight : document.body.clientHeight)+(ignoreMe = document.documentElement.scrollTop ? document.documentElement.scrollTop : document.body.scrollTop))+'px');
    }

# 旋转图片

相比加载一个新的图片，旋转图片可以达到同样的效果。例如现在仅仅只有一张箭头的图片，但我们需要指向不同的方向箭头图片。现在通过旋转你的问题就解决了。

    img.flip {
        -moz-transform: scaleX(-1);
        -o-transform: scaleX(-1);
        -webkit-transform: scaleX(-1);
        transform: scaleX(-1);
        filter: FlipH;
        -ms-filter: "FlipH";
    }

# 清除浮动

如何不添加任何额外的标记文件来清除浮动元素？
通过创建一个class，可以清除相应容器中的浮动子元素。

    .clearfix:after {
        visibility: hidden;
        display: block;
        font-size: 0;
        content: " ";
        clear: both;
        height: 0;
    }
    
    .clearfix { display: inline-block; }
    
    /* start commented backslash hack \*/
    * html .clearfix { height: 1%; }
    .clearfix { display: block; }
    /* close commented backslash hack */
    CSS3实现圆角
    
    在比较新的浏览器中，通过CSS3很容易实现圆角。仅仅>IE9的浏览器支持这个属性。
    
    .round{
        -moz-border-radius: 10px;
        -webkit-border-radius: 10px;
        border-radius: 10px; /* future proofing */
        -khtml-border-radius: 10px; /* for old Konqueror browsers */
    }

# 覆盖样式

CSS中的!important非常的强大而且也非常的有用。通过使用!important可以覆盖任何位置的样式，无论它们出现在CSS中，还是HTML页面中。

    p{
        font-size:20px !important;
    }

# 本地字体

github网站把常用的小图标都压缩一个字体，通过font-face载入，这样加快了整个网站加载速度、减少了请求数量，只需要请求一个字体，所有图标都出来了。而且图标的颜色可以根据你设置字体的颜色很方便改变。

    @font-face{
        font-family: 'Graublau Web';
        src: url('GraublauWeb.eot');
        src: local('☺'),
        url('GraublauWeb.woff') format('woff'), url('GraublauWeb.ttf') format('truetype');
    }

# 居中网站

通常我们的设计的网页都是居中浏览器显示的。

    .wrapper{
        width:960px;
        margin:0 auto;
    }

IE6实现最小高度

大部分浏览器直接使用min-height去实现最小高即可，但是ie6不识别，接下来我们要解决的只有ie6的问题。我们主要使用ie6不识别的min-height和!important来解决这个问题。

    .box{
        min-height:500px;
        height:auto !important;
        height:500px;
    }

# 图片加载效果

使用图片加载效果模范AJAX加载。当加载目标图片的时候，回提前显示img定义加载图片。这种效果针对加载比较大的图片的时候非常有效。

    img {
        background: url(loader.gif) no−repeat 50% 50%;
    }

# 垂直居中

下边的方法是不通过添加额外的标记去实现一个元素垂直居中的效果，只需将这个元素显示为一个表格单元格，然后允许您使用vertical-align属性。

    .container{
        min-height: 10em;
        display: table-cell;
        vertical-align: middle;
    }

# 创建引用

网站需要突出表达的一些东西将使用blockquote区别显示出来。

    .pullquote {
        width: 300px;
        float: right;
        margin: 5px;
        font-family: Georgia, "Times New Roman", Times, serif;
        font: italic bold #ff0000 ;
    }

# 文本选择

很多人看一段文字会把这段文字用鼠标选中，那段文字会有个选中样式，Text Selection就是改变那个鼠标选中后的效果。

    ::selection {
    color: #000000;
    background-color: #FF0000;
    }
    ::-moz-selection {
    color: #000000;
    background: #FF0000;
    }

# 打印分页符

这个属性是在当打印网页的时候会用到。

    .page-break{
        page-break-before:always;
    }

原文：[http://www.1stwebdesigner.com/design/useful-css-snippets/](http://www.1stwebdesigner.com/design/useful-css-snippets/)