---
title: "解决firefox 无法渲染icon fonts的问题"
categories: [ "Css" ]
tags: [ "icon","fonts" ]
draft: false
slug: "solve-the-problem-of-firefox-fonts-icon-can-not-render"
date: "2015-08-20 10:35:00"
---

![20150820102655.png][1]
今天遇到个小问题，和下图的问题类似：出现类似十六进制乱码"" , content: "\e6b9";

![LNN9p.png][2]
以这种方式写的字体 将不被firefox 处理。


<!--more-->


    @font-face
    {
      font-family:'FontAwesome';
      src:url('./fontawesome-webfont.eot');
      src:url('./fontawesome-webfont.eot?#iefix') format('embedded-opentype'),
          url('./fontawesome-webfont.woff') format('woff'),
          url('./fontawesome-webfont.ttf') format('truetype'),
          url('./fontawesome-webfont.svg#FontAwesome') format('svg');
      font-weight:normal;
      font-style:normal
    }
    
    [class^="icon-"]:before,
    [class*=" icon-"]:before
    {
      font-family:FontAwesome;
      font-weight:normal;
      font-style:normal;
      display:inline-block;
      text-decoration:inherit
    }

这是由于firefox 不识别`.` 所导致的，应该将路径改成这样子。
 `./ 改成 /fonts/` (例如`./fontawesome-webfont.ttf` 改成 `/fonts/fontawesome-webfont.ttf`).

哈哈 还有另一个问题，就是你开启了firefox的硬件渲染，
`firefox 工具->选项->高级 自动启用硬件加速` 开启了 
如下图：
![20150820102542.png][3]


  [1]: https://imgs.gnux.cn/usr/uploads/2015/08/2425877222.png
  [2]: https://imgs.gnux.cn/usr/uploads/2015/08/10399572.png
  [3]: https://imgs.gnux.cn/usr/uploads/2015/08/3761802285.png