---
title: 轻量级的代码高亮插件:Prism
date: 2014-10-13 17:32:00
updated: 2015-10-02 17:33:26
tags: 
- vagrant
- unitest
categories: 
- js

---
Prism是一个轻量级并且简单易用的JavasScript类库，经过压缩之后1.5Kb左右的大小，我们也可以自定义需要的语言和功能，同样也提供主题的功能
## Prism介绍

Prismz只需要添加JavaScript和CSS就能使用。它默认包含了Hhtml\CSS\JavaScript三个语言，你也可以选择添加其他语言，现在提供了PHP、Python、C、C#等多种语言。另外还提供了插件功能，比如行号，行内高亮，自动链接，显示语言功能，如果有自己的需求，也可以DIY你自己的插件。

## Prism使用


<!--more-->


访问 [http://prismjs.com/download.html](http://prismjs.com/download.html)定制你的插件，然后下载Js和CSS文件。

1.在页面的head中引入Prism的CSS文件

    <link href="prism.css" rel="stylesheet" />

2.在</body>之前引入Js文件

    <script src="prism.min.js"></script>

3.需要高亮显示的代码要放在pre标签中，设置code标签的class为代码的名称，比如：

    <pre><code class="language-xxx"> ... </code></pre>