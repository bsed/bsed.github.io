---
title: "Highlightjs 添加代码行号 "
categories: [ "JS" ]
tags: [ "Highlightjs" ]
draft: false
slug: "754"
date: "2013-10-30 14:02:00"
---

Highlightjs是一款优秀的代码高亮Js组件，可以很方便地对各种语言编写的代码添加语法高亮样式。

然而，Highlightjs默认是不包括显示代码行号（Line Number）这一特性的，不过可以通过简单的JS代码和CSS样式表实现对Highlightjs的行号显示功能。

参考博文链接：[syntax-highlighting-with-highlightjs](http://idodev.co.uk/2013/03/syntax-highlighting-with-highlightjs/)


<!--more-->


Javascript代码：

    //numbering for pre>code blocks
    //引入jQuery1.9.3库
    $(function(){
        $('pre code').each(function(){
            var lines = $(this).text().split('\n').length - 1;
            var $numbering = $('<ul/>').addClass('pre-numbering');
            $(this)
                .addClass('has-numbering')
                .parent()
                .append($numbering);
            for(i=1;i<=lines;i++){
                $numbering.append($('<li/>').text(i));
            }
        });
    });

实际上很简单，首先找到代码块$('pre code')。然后取得内容并按照换行符split，我们可以得到代码块的行数。最后，我们构建一个包含从数字1到行数n的行号列表，然后把这个列表追加到</pre>和</code>的闭合标签之间。

此外通过添加适当的css样式将行号列表渲染到代码的左手边缘。使用时请结合页面样式对下面的css做出相应修改。

    pre {
        position: relative;
        margin-bottom: 24px;
        border-radius: 3px;
        border: 1px solid #C3CCD0;
        background: #FFF;
        overflow: hidden;
    }
    
    code {
      display: block;
      padding: 12px 24px;
      overflow-y: auto;
      font-weight: 300;
      font-family: Menlo, monospace;
      font-size: 0.8em;
    }
    
    code.has-numbering {
        margin-left: 21px;
    }
    
    .pre-numbering {
        position: absolute;
        top: 0;
        left: 0;
        width: 20px;
        padding: 12px 2px 12px 0;
        border-right: 1px solid #C3CCD0;
        border-radius: 3px 0 0 3px;
        background-color: #EEE;
        text-align: right;
        font-family: Menlo, monospace;
        font-size: 0.8em;
        color: #AAA;
    }

通过上面的js代码和css样式表，即可完成简单易用的Highlightjs行号显示。
效果如图:
![20150103140442.png][1]


  [1]: https://imgs.gnux.cn/usr/uploads/2015/01/831826569.png