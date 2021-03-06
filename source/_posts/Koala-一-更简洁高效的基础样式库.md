---
title: "Koala 一 更简洁高效的基础样式库"
categories: [ "Css" ]
tags: [ "css","normalize","koala" ]
draft: false
slug: "koala-a-more-concise-and-efficient-foundation-style-library"
date: "2015-08-06 15:07:00"
---

### 一、关于 Koala

Koala 是一套基础样式库，它是基于Normalize.css内容延伸的一个产物，继承其优势来扩展出更多实用性解决方案。

基础样式库 始终 能帮我们解决开发时遇到的一些基础性问题：

如浏览器样式不统一，需要重置样式；
如基础表单样式过于难看不易操作，需要优化其样式；
如布局样式需要重复编写，需要一套稳定复用的解决方案；
如一些样式的浮动，对齐操作，需要统一管理起来进行多次复用；
如常见的CSS动画效果，需要统一起来管理方便复用；
如一些常用的图标，需要一套可复用的字体图标；
如需要打印，提供一套更为合理的打印样式
初衷和未来方向：


<!--more-->


最大程度减少开发难度
提供简洁高效开发体验
趋于模块化样式构建规范
做为未来UI组件库核心基础
始终站在巨人肩上
#### 二、目录结构：

    ---
     |---- src/           源码
     |---- README.md

### 三、文件内容：

    normal.css   [基于Normalize.css自定义模块，继承其优势，改善文字与部分细节]
    html5.css    [html5样式修复，默认会导入normal.css，可按需引用]
    form.css     [表单的一些基础样式，可按需引用]   
    grid.css     [响应式网格系统，优化命名与精简代码，可按需引用]   
    utils.css    [HTML中直接使用的工具类，可按需引用] 
    iconfont.css [一套复用率极高的字体图标，可按需引用] 
    animate.css  [常用的动画效果组合，可按需引用] 
    print.css    [优化默认的打印样式，可按需使用]

### 四、说明：

当前版本并不是正式生产版本，所以还不能完全应用到项目中，目前仅供学习参考使用，部分的细节完善和优化还在进行中，如果你有意见和问题，欢迎随时联系：chyi722@163.com

项目地址：[https://github.com/Alsiso/Koala](https://github.com/Alsiso/Koala)