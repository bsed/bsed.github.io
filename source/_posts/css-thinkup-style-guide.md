---
title: Thinkup CSS 风格指南
date: 2016-10-05 09:29:00
updated: 2016-10-05 10:25:00
tags: 
- atom
- racer
- rust
categories: 
- linux

---
> 这是CSS代码风格指南。其他语言的代码风格指南。

## 缩进

使用两个空格的缩进而不是用 tab。

## 命名和大小写

id 应该用 首字母小写的驼峰命名法。


<!--more-->


```css
#pageContainer {
```

class(类名)应该是小写,单词下划线隔开。

```css
.my_class_name {
HTML元素应该是小写的。

body, div {
使用外联样式表
```

不要编写内联样式或者嵌入式样式,除非不可避免的。内联或嵌入样式最有可能是可以避免的——问ThinkUp邮件列表,如果你不确定。

出于性能方面的原因(见 [Steve Souder’s blog](http://www.stevesouders.com/blog/2009/04/09/dont-use-import/)),总是链接外部样式表使用`link`语法而不是`import`语法。

```css
<link rel="stylesheet" href="a.css"> <!-- Okay -->
<style type="text/css">@import url("a.css");</style> <!-- Not Okay -->
```
## 写有效的CSS

ThinkUp CSS应该有效的[CSS 2.1规范](http://www.w3.org/TR/CSS2/)。CSS 3.0规则是可以接受的,只要他们完全降低。

确保页面完成之前，运行[W3C validator](http://jigsaw.w3.org/css-validator/)校验工具进行校验。

## 注释

强烈建议给页面添加注释。关心注释影响性能?别担心,可以由CSS缩小工具在生产服务器上使用以便于删除注释。

注释是指选择器块应立即在一个单独的行之前添加注释参考。短的内联注释可以添加一对属性后,与属性之间用空格隔开。

```css
/* Comment about this selector block. */
selector {
  property: value; /* Comment about this property-value pair. */
}
```

只有C风格的注释(/* Comment goes here. */)是有效的。不要使用c++风格的注释(// Comment goes here.)。


## 选择器

选择器要另起一行，最后一个选择器要隔一个空格，后面跟着一个开括号。选择器模块应该结束了一个花括号，unindented并在一个单独的一行中。每个选择器块之间应设置一个空白行。选择器不能缩进。
```css
selector {
}

selector {
}
```

多个选择器每个应该另起一行,每个逗号后没有空格。

```css
selector1,
selector2,
selector3,
selector4 {
}
```

当选择HTML元素,写小写的选择器。

```css
div { /* Okay */
DIV { /* Not okay */
```

## 属性-值

属性-值应该列出左括号后开始的，要成对出现。

 - 每个属性一行
 - 缩进一个水平线;
 - 在冒号后面有一个空格,将属性名与属性值;然后以分号结束。

```css
selector {
  name: value;
  name: value;
}
```
属性-值的多个属性应该按字母顺序列出。
```css
/* Not okay */
body {
  font-weight: normal;
  width: 500px;
  background: #000;
}

/* Okay */
body {
  background: #000;
  font-weight: normal;
  width: 500px;
}
```
属性有多个值,用一个空格来分隔每一个值后,使用逗号(,)。
```css
  font-family: Helvetica, sans-serif;
```
如果一个值包含任何空间,该值必须包含在双引号内。
```css
  font-family: "Lucida Grande", Helvetica, sans-serif;
```

## 颜色

当使用十六进制符号表示颜色,使用大写字母。三位和6位十六进制符号都是可以接受的,如果可能的话使用三位十六进制符号来指定想要的颜色,所以你可以节省终端用户几个字节的下载时间。

```css
  color: #FFF;    /* Okay */
  color: #FE9848; /* Okay */
  color: #fff;    /* Not okay */
```

## 尺寸

当表示维度——也就是说,一个元素的宽度或高度,或其margin,border,或padding,指定单位在em,px或者%。如果宽度或高度的值是0,未指定单位。
```css
  width: 12px;  /* Okay */
  width: 12%;   /* Okay */
  width: 12em;  /* Okay */
  width: 12rem; /* Okay */
  width: 0;     /* Okay */
  width: 12;    /* Not okay */
  width: 0px;   /* Not okay */
```