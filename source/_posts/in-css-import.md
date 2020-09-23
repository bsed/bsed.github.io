---
title: CSS中的@import
date: 2013-04-16 21:53:00
updated: 2015-01-09 22:03:54
tags: 
- css
- import
categories: 
- css

---
我们知道，css文件引入方式有两种：

 1. HTML中使用link标签
```
    <link rel="stylesheet" href="style.css" />
```
 2. CSS中@import
```
    @import "style.css";
```
第一种方式最为常见最为主流，第二种方式则很少见到有人这么写，因而也常被开发工程师所忽略。这篇文章就详细解剖之。


----------


##  语法  ##
@import语法有两种：

    @import "style.css";
    @import url("style.css");

这两种语法并没什么差别。
##  规则  ##
[MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/@import)中这么描述@import：

> The @import CSS at-rule allows to import style rules from other style
> sheets. **These rules must precede all other types of rules, except
> @charset rules**; as it is not a nested statement, it cannot be used
> inside conditional group at-rules.

注意到加粗的部分么——**import规则一定要先于除了@charset的其他任何CSS规则**，这句话是什么意思呢，我们看个例子：

index.html:

    <style type="text/css">
      .hd{
        color: orange;
      }
      @import "import.css";
    </style>
    ...
    <p class="hd">我是什么颜色</p>

import.css:

    .hd{
        color: blue;
      }

测试发现，p的颜色并不是import.css里所定义的蓝色，而是之前定义的橘黄色。打开网络请求会发现没有请求import.css文件，这正是因为，再次强调一遍，**import规则一定要先于除了@charset的其他任何CSS规则**，所以需要将index.html改成酱紫：

    <style type="text/css">
      @import "import.css";
      .hd{
        color: orange;
      }
    </style>
    ...
    <p class="hd">我是什么颜色</p>

这时候能看到import.css网络请求，p的颜色为橘黄色，覆盖了import.css里定义的蓝色。

##  媒体查询  ##
`@import`和`link`一样，同样可以定义媒体查询(media queries)，

我们先看看link定义的方式：

    <link rel="stylesheet" type="text/css" href="print.css" media="print"/>

接下来是@import：

    @import url("print.css") print;
    @import "common.css" screen, projection;
    @import url('landscape.css') screen and (orientation:landscape);
    @import url('mobile.css') (max-width: 680px);

这里要注意的是，**不论是link还是import方式，会下载所有css文件**，然后根据媒体去应用css样式，而不是根据媒体去选择性下载css文件。

##  不要使用@import  ##
这。。坑爹呢，看了一大堆，结果告诉我不要使用！
这也只是个建议，因为import的确会带来一些问题，所以网络上会有各种「抵制@import」的文章，既然设计了@import，总有它的有用之处，不能过于绝对。使用@import影响页面性能的地方主要体现在两个方面：

 1. 影响浏览器的并行下载
 2. 多个@import导致下载顺序紊乱

具体可以看看[高性能网站设计：不要使用@import这篇文章](http://www.qianduan.net/high-performance-web-site-do-not-use-import.html)，这里就不多说，看完后记得回来点个赞或收藏：）。