---
title: 神器的css中的居中问题
date: 2015-11-12 18:17:00
updated: 2018-11-14 10:19:55
tags: 
- 根基
- 民生
categories: 
- default

---
看到一个总结很全的居中，本文是其部分译文，[Absolute Centering](http://codepen.io/shshaw/details/gEiDt)

## 一、负边距法

这是最常用也是唯一兼容IE6/7的方法

```css
 .is-Negative {
        width: 300px;
        height: 200px;
        padding: 20px;
        position: absolute;
        top: 50%; left: 50%;
        margin-left: -170px; /* (width + padding)/2 */
        margin-top: -120px; /* (height + padding)/2 */
}
```


<!--more-->


但这种方法有一个很明显的不足，就是需要提前知道元素的尺寸。否则margin负值的调整无法精确。此时，往往要借助JS获得。

优点:
支持所有的浏览器，包括IE6/7；
代码量较少
缺点:
非响应式。不基于百分比尺寸工作，不能设置MIN-/ MAX-
内容可溢出容器
需要用 padding来补偿 margin 或者使用 box-sizing: border-box

## 二、translate偏移法

Css3中可以使用transform代替margin. transform中translate偏移的百分比值是相对于自身大小的，于是，我们可以：

```css
.is-Transformed {   
  width: 50%;  
  margin: auto;  
  position: absolute;  
  top: 50%; left: 50%;  
  -webkit-transform: translate(-50%,-50%);  
      -ms-transform: translate(-50%,-50%);  
          transform: translate(-50%,-50%);  
}  
```

优点：

1. 内容可变高度
2. 代码量少
   缺点：
3. IE8不支持
4. 属性需要写浏览器厂商前缀
5. 可能干扰其他transform效果
6. 某些情形下会出现文本或元素边界渲染模糊的现象

## 三、表格单元格（Table-Cell）

总的说来这可能是最好的居中实现方法，因为内容块高度会随着实际内容的高度变化，浏览器对此的兼容性也好。最大的缺点是需要大量额外的标记，需要三层元素让最内层的元素居中。
HTML：

```html
<div class="Center-Container is-Table">  
  <div class="Table-Cell">  
    <div class="Center-Block">  
    </div>  
  </div>  
</div>
```

CSS：

```css
.Center-Container.is-Table { display: table; }  
.is-Table .Table-Cell {  
  display: table-cell;  
  vertical-align: middle;  
}  
.is-Table .Center-Block {  
  width: 50%;  
  margin: 0 auto;  
}  
```

优点：

1. 高度可变
2. 内容溢出会将父元素撑开。
3. 跨浏览器兼容性好。
   缺点：

需要额外html标记

## 四、margin:auto实现绝对定位元素的居中

```css
.Absolute-Center {
  margin: auto;
  position: absolute;
  top: 0; left: 0; bottom: 0; right: 0;
}
 
```

解释：
通过以上描述，绝对居中（AbsoluteCentering）的工作机理可以阐述如下：
1、在普通内容流（normal content flow）中，margin:auto的效果等同于
margin-top:0;margin-bottom:0。
2、position:absolute使绝对定位块跳出了内容流，内容流中的其余部分渲染时3、为块区域设置top:  0; left: 0; bottom: 0; right:  0;将给浏览器重新分配一个边界框，此时该块block将填充其父元素的所有可用空间，父元素一般为body或者声明为position:relative;的容器。
4、  给内容块设置一个高度height或宽度width，能够防止内容块占据所有的可用空间，促使浏览器根据新的边界框重新计算margin:auto
5、由于内容块被绝对定位，脱离了正常的内容流，浏览器会给margin-top,margin-bottom相同的值，使元素块在先前定义的边界内居中。

优点：
1.支持跨浏览器，包括IE8-IE10.
2.无需其他特殊标记，CSS代码量少
3.支持百分比%属性值和min-/max-属性
4.只用这一个类可实现任何内容块居中
5.不论是否设置padding都可居中（在不使用box-sizing属性的前提下）
6.内容块可以被重绘。
7.完美支持图片居中。
缺点：
1.必须声明高度（查看可变高度Variable Height）。
2.建议设置overflow:auto来防止内容越界溢出。
3.在Windows Phone设备上不起作用。

## 五、Flexbox

这是CSS布局未来的趋势。Flexbox是CSS3新增属性，设计初衷是为了解决像垂直居中这样的常见布局问题。相关的文章如《Centering Elements with Flexbox》
记住Flexbox不只是用于居中，也可以分栏或者解决一些令人抓狂的布局问题。

```css
.Center-Container.is-Flexbox {
  display: -webkit-box;
  display: -moz-box;
  display: -ms-flexbox;
  display: -webkit-flex;
  display: flex;
  -webkit-box-align: center;
     -moz-box-align: center;
     -ms-flex-align: center;
  -webkit-align-items: center;
          align-items: center;
  -webkit-box-pack: center;
     -moz-box-pack: center;
     -ms-flex-pack: center;
  -webkit-justify-content: center;
          justify-content: center;
}
 
```

优点：
内容块的宽高任意，优雅的溢出。
可用于更复杂高级的布局技术中。
缺点：

1. IE8/IE9不支持。
2. Body需要特定的容器和CSS样式。
3. 运行于现代浏览器上的代码需要浏览器厂商前缀。
4. 表现上可能会有一些问题