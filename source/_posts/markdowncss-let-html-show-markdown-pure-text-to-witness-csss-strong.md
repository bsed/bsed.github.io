---
title: markdown.css 让 HTML 显示成 Markdown 纯文本 见证 CSS 的强大
date: 2015-09-30 19:30:00
updated: 2016-06-02 11:33:26
tags: 
- 江南
categories: 
- default

---
[Markdown.css](http://mrcoles.com/demo/markdown-css/) 是一个很有意思的 CSS 样式表。它可以让 HTML 显示成类似 Markdown 纯文本的形式。（图片除外）。

## 基于 LESS 编写

Markdown.css 基于 LESS 编写，[源代码在此](https://github.com/mrcoles/markdown-css/blob/master/markdown.less)。

下面简要地解析一下具体实现的方法。

### 标题


<!--more-->


markdown 的标题在前加上 `#` 到 `######` 表示。这是用了 CSS 的伪类 :before 实现，例如 h2 是这么定义的：

    h2:before {
      content: "## ";
      display: inline;
    }

### 强调
强调是前后加上*，因此除了 `:before` 之外还用到了 `:after`。

    @em-char: "*";
    em:before, em:after {
      content: @em-char;
      display: inline;
    }

`strong`、`code`的实现类似。

### 链接

Markdown 中的链接采用 `[text](link)` 的格式，实现和上面的强调的做法类似，首先去掉文本的装饰，然后使用 :before 在前面添加 `[`:

    a {
      text-decoration: none;
    }
    a:before {
      content: "[";
      display: inline;
      color: @color;
    }

后面添加的内容中包含链接，可以通过 attr(href) 取得：

    a:after {
      content: ~'"](" attr(href) ")"';
      display: inline;
      color: @color;
    }

#### pre

pre 的话，很简单，只要左边缩进四个字符即可。对于支持缩进四个字符的浏览器，使用4ch即可，不支持的浏览器那就使用`34px`。

    @four-space: 34px;
    @four-space-css3: ~"4ch"; 
    pre {
      margin-left: @four-space;
      padding-left: @four-space-css3;
    }

### 引用

Markdown 中的引用采用如下格式：

    > 这是一个引用
    > 引用的第二行

因此，采用的方法是在引用后添加 `>` 和 `\A` （换行），然后调整位置，使其与原文“对齐”，并隐藏多余的>。

    blockquote {
      position: relative;
      padding-left: @four-space/2;
      padding-left: @two-space-css3;
      overflow: hidden;
    
      &:after {
          // 100 lines max
          // the \A becomes a newline character and `white-space: pre`
          // makes it act like a <br>
          content: ">\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>\A>";
          white-space: pre;
          position: absolute;
          top: 0;
          left: 0;
          font-size: @font-size;
          line-height: @line-height;
      }
    }

### 图片

前面已经说过图片会照常显示。 感谢 [jakwings](http://blog.likelikeslike.com/) 提示，使用如下 CSS 可以实现图片转为 markdown 形式显示：

    img { content: "" } 
    
    img:before {
     content: "![";
     color: #333333;
    }
    
    img:after {
     content: "](" attr(src) ")";
     color: #333333;
    }

### markdownify

还有一个 markdownify 的 bookmarklet，将其保存在书签栏之后，可以将任意网站转为 Markdown 样式。

    $('link[rel=stylesheet]').add('style').remove();
    $('[style]').attr('style', '');
    $('head').append('<link rel="stylesheet" href="http://mrcoles.com/media/test/markdown-css/markdown.css" type="text/css" />');
    $('body').addClass('markdown').css({width: '600px', margin: '2em auto', 'word-wrap': 'break-word'});
    $('a img').css({'max-height': '1em', 'max-width': '1em'});

原文：[http://segmentfault.com/a/1190000000487062](http://segmentfault.com/a/1190000000487062)