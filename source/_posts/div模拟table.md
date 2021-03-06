---
title: "div模拟table"
categories: [ "Css" ]
tags: [ "css","table" ]
draft: false
slug: "2627"
date: "2013-06-04 14:06:00"
---

许多网页设计师都喜欢，将两个或者多个容器等高的并排放置，并在里面展示每个容器的内容，就象经典表格布局中的单元格控制几个栏目的位置，也喜欢容器的内容居中或顶部对齐显示。

但是你又不喜欢用table来实现他，那怎么办呢？实现的方法很多，有根据视觉错觉实现的，有用JS控制使高度相等的，还有采用容器溢出部分隐藏和列的负底边界和正的内补丁相结合的方法来解决列高度相同的问题。

其实有个简单的方法，使用display:table, display:table-row and display:table-cell 就可以实现，而且高度小的容器会自适应那些高度相对较高的，但是IE不支持这个属性，我们先不用去责备IE，相信以后会有所改善的。这里我制作了一个模型。

## 像table一样布局div


<!--more-->


先看看xhtml的结构：
```html
<div class="equal">
    <div class="row">
        <div class="one"></div>
        <div class="two"></div>
        <div class="three"></div>
    </div>
</div>
```
　　很简单不用解释就能看懂，但是这里给出一个table的结构，是不是很相似
```html
<table>
 <tr>
  <td></td>
  <td></td>
  <td></td>
 </tr>
</table>
```
下来是css：
```css
 .equal {
  display:table;
  border-collapse:separate;
 }
 .row {
  display:table-row;
 }
 .row div {
  display:table-cell;
 }
 .row .one {
  width:200px;
 }
 .row .two {
  width:200px;
 }
 .row .three {
  
 }
```
**解释**：

　　1.dispaly:table;让层.equal作为块级元素的表格table显示，也就是将他作为一个表格
　　2.border-collapse:separate;边框独立，就像表格没有合并单元格以前
　　3.display:table-row;将.row作为表格行tr显示
　　4.display:table-cell;将.row的下级div作为表格单元格td显示
　　5.然后定义宽度

这里还使用了 border-spacing:10px;来区别几个盒子，正如上面所陈述的，IE下不能正常显示，但是在：Mozilla 1.6, Opera 7.50, Safari 1.2.2, Firefox 0.8, OmniWeb 5b, Camino 0.8b, and Netscape 7.1经过测试均可以完美显示.

## 像table一样布局div(2)回顶部
上面的问题就是，这个模型对IE来说等同于垃圾，所以基本只能是做来玩玩而已，没有什么实际的用处，现在我要做的就是，让它也能在IE下更好的显示,所以我又做了第二个模型

xhtml结构类似第一个模型 的只是增加一个新的div给IE
```html
<div class="equal">
    <div class="row">
        <div class="one"></div>
        <div class="two"></div>
        <div class="three"></div>
        <!--[if IE]>
        <div class="ieclearer"></div>
        <![endif]-->
    </div>
</div>
```
给xhtml增加一个判断，判断是否为IE，然后给IE一个特殊待遇，在IE显示和别的浏览器不同的代码，对于这个判断IE\MAC优先选择显示其间的内容。

然后在CSS中也增加一个判断，配合xhtml,用浮动对齐（浮动对齐就不用解释了）的方式来实现容器的等高并排放置，但是还是无法真实的实现等高，你可以采用添加背景颜色的视觉错觉来实现视觉上的等高，或者采用别的方法，这里就不进行说明了。

下面是CSS中的代码
```css
<!--[if IE]>
    <style type="text/css" media="all">
.equal, .row {
    display:block;
}
.row {
    padding:10px;
}
.row div {
    display:block;
    float:left;
    margin:0;
}
.row .two {
    margin-left:10px;
}
.row .three {
    width:160px;
    float:right;
}
.ieclearer {
    float:none;
    clear:both;
    height:0;
    padding:0;
    font-size: 2px;
    line-height:0;
}
    </style>
<![endif]-->
```

到此，整个设计真正的完成了，虽然不够完美，对非IE而做出努力显得有点多余，现在要么IE支持第一个模型，要么我们可能会想出更好的方案来。但是这次尝试是有价值的。

## 另一个例子:

CSS：
```css
.head{
    display: table;
    position: relative;
    margin: 10px 0;
    width: 100%;
}
.head div{
    height:100%;display: table-cell;
    vertical-align: bottom;
}
.head span{
    color: #AC5697;
}
.toLeft{
    text-align: left;
}
.toRight{
    text-align: right;
}
```
HTML：
```html
<div class="head">
    <div class="toLeft"><span>车牌号 : </span>粤H21643</div>
    <div class="toRight"><span>检票 : </span>截至目前为止还差4人需要检票</div>
</div>
```
看clss选择器 head，在父级DIV使用了`display: table;` 它的子元素则是 `display: table-cell;`

这样做就会达到模拟table的效果，注意这里我只是说模拟table的效果并非真的做成table（那还不如直接写table），

这样做的好处：在同一行里显示连个元素。

当然display为inline也是可以一行显示一个以上元素，但是这样width：100%；会失效。

加float属性也可以，但是缺点是：同行的元素的无关联性，比如左边的元素由于内容过多，已经将height撑到50px；这时右边的两个字还是停留在20px