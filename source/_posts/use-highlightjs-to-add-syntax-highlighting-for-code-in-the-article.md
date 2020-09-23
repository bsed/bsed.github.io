---
title: 用 highlight.js 为文章中的代码添加语法高亮
date: 2014-02-17 22:29:00
updated: 2015-09-30 22:31:52
tags: 
- jquery
- ajax
categories: 
- js

---
## 引入 [highlight.js](http://highlightjs.org/) 文件

我们需要在页面中同时引入一个 js 文件和一个 css 文件（在这里我们省点儿力气，直接使用 Bootstrap中文网 提供的 CDN 链接，既简单又提升加载速度）：

    <link href="http://cdn.bootcss.com/highlight.js/8.0/styles/monokai_sublime.min.css" rel="stylesheet">  
    <script src="http://cdn.bootcss.com/highlight.js/8.0/highlight.min.js"></script>  

第一个文件是样式文件，我们采用 sublime 编辑器类似的样式；第二个文件是 highlight.js 的核心文件，就是它解析代码的。
在添加这两个文件的时候，我们还是按老规矩，将 CSS 文件添加到 <head> 中，将 js 文件添加到页面底部 </body> 标签前面，这样就完成了 highlight.js 的引入。


<!--more-->


**注：**你可以在本页面点击右键 -> 查看源码，找到上面提到的两个文件，然后照葫芦画瓢就行。
让代码高亮吧

前面的准备工作做完了，现在还有最后一步，就是：调用 hihglight.js 解析代码，完成高亮工作。

在 `<script src="http://cdn.bootcss.com/highlight.js/8.0/highlight.min.js"></script>` 标签下面加入如下代码：

    <script >hljs.initHighlightingOnLoad();</script>  

这样，highlight.js 就会自动寻找 < pre><code></code></pre > 代码块并完成代码高亮了！

书写 markdown 时的注意事项

对于大段的代码建议采用 gfm 格式，如下：

    ```
    这里放代码
    ```
highlight.js 可以自动识代码所用的编程语言，但是，电脑不如人脑啊！识别错误的几率很高，所以，为了安全起见，最好标识一下所用的编程语言，例如：

    ```html
    <DOCTYPE html>
    <html>
    </html>
    ```
前面说过，highlight.js 支持 71 种编程语言（完整列表看这里），既包括常见的 C、java、javascript、ruby、python、html、css等，还支持 apache、nginx 这些配置文件的语法，所以，在你书写这些代码的时候，可以看看 highlight.js 是否支持所用的语法，然后标注对应的语言，让 highlight.js 完美的对其高亮显示吧。