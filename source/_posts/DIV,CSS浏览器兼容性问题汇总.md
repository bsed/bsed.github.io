---
title: "DIV,CSS浏览器兼容性问题汇总"
categories: [ "Css" ]
tags: [ "css","html","兼容" ]
draft: false
slug: "div-summary-of-css-browser-compatibility-issues"
date: "2016-02-27 11:41:00"
---

> CSS对浏览器的兼容性有时让人很头疼,或许当你了解当中的技巧跟原理,就会觉得也不是难事,从网上收集了IE7,6与Fireofx的兼容性处理方法并整理了一下

请尽量用xhtml格式写代码，而且DOCTYPE影响 CSS 处理，作为W3C标准，一定要加DOCTYPE声明。

## 1.div的垂直居中问题

`vertical-align:middle;` 将行距增加到和整个DIV一样高 `line-height:200px;` 然后插入文字，就垂直居中了。缺点是要控制内容不要换行 

## 2. margin加倍的问题

设置为float的div在ie下设置的margin会加倍。这是一个ie6都存在的bug。解决方案是在这个div里面加上`display:inline;`

**例如：**

```html
    <#div id="imfloat">
```
相应的css为:

```css
    #imfloat{
    float:left;
    margin:5px;
    display:inline;
}
```
## 3.浮动ie产生的双倍距离

```css
#box{ 
   float:left;
   width:100px;
   margin:0 0 0 100px; //这种情况之下IE会产生200px的距离 
   display:inline; //使浮动忽略
}
```
这里细说一下block与inline两个元素：block元素的特点是,总是在新行上开始,高度,宽度,行高,边距都可以控制(块元素);Inline元素的特点是,和其他元素在同一行上,不可控制(内嵌元素);

```css
#box
 { display:block; //可以为内嵌元素模拟为块元素 
   display:inline; //实现同一行排列的效果 
  diplay:table;
}
```

## 4 IE与CSS宽度和CSS高度的问题div css技巧

IE不认得min-这个定义，但实际上它把正常的width和height当作有min的情况来使。这样问题就大了，如果只用宽度和高度，正常的浏览器里这两个值就不会变，如果只用min-width和min-height的话，IE下面根本等于没有设置宽度和高度。

比如要设置背景图片，这个宽度是比较重要的。要解决这个问题，可以这样：
```css
#box {
 width: 80px; 
 height: 35px;
}
html>body #box { 
 width: auto; 
 height: auto; 
 min-width: 80px; 
 min-height: 35px;
}
```
## 5.页面的最小宽度

min-width是个非常方便的CSS命令，它可以指定元素最小也不能小于某个宽度，这样就能保证排版一直正确。但IE不认得这个，而它实际上把 width当做最小宽度来使。为了让这一命令在IE上也能用，可以把一个＜div> 放到＜body> 标签下，然后为div指定一个类,然后CSS这样设计：

```css
#container{ 
 min-width: 600px;
 width:expression_r(document.body.clientWidth ＜ 600? "600px": "auto" );
}
```
第一个min-width是正常的；css制作但第2行的width使用了Javascript，这只有IE才认得，这也会让你的HTML文档不太正规。它实际上通过Javascript的判断来实现最小宽度。

## 6.DIV浮动IE文本产生3象素的bug

左边对象浮动，右边采用**外补丁的左边距来定位**，右边对象内的文本会离左边有3px的间距.

```css
#box{ 
 float:left;
 width:800px;
}
#left{
 float:left;
 width:50%;
}
#right{
 width:50%;
}
*html #left{ 
 margin-right:-3px; //这句是关键
}
```

```html
<div id="box">
<div id="left">＜/div>
<div id="right">＜/div>
</div>
```
## 7.IE捉迷藏的问题

当div应用复杂的时候每个栏中又有一些链接，DIV等这个时候容易发生捉迷藏的问题。

有些内容显示不出来，当鼠标选择这个区域是发现内容确实在页面。**解决办法**：对#layout使用line-height属性或者给#layout使用固定高和宽。页面结构尽量简单。


## 8.float的div闭合;清除浮动;自适应高度

①例如：`＜#div id="floatA" >＜#div id="floatB" >＜#div id="NOTfloatC" >`这里的NOTfloatC并不希望继续平移，而是希望往下排。(其中floatA、floatB的属性已经设置为float:left;)

这段代码在IE中毫无问题，问题出在FF。原因是NOTfloatC并非float标签，必须将float标签闭合。在 `＜#div class="floatB"> ＜#div class="NOTfloatC">`之间加上 `＜#div class="clear">`这个div一定要注意位置，而且必须与两个具有float属性的div同级，之间不能存在嵌套关系，否则会产生异常。并且将clear这种样式定义为为如下即可： .clear{ clear:both;}

②作为外部 wrapper 的 div 不要定死高度,div css制作为了让高度能自动适应，要在wrapper里面加上overflow:hidden; 当包含float的box的时候，高度自动适应在IE下无效，这时候应该触发IE的layout私有属性(万恶的IE啊！)用zoom:1;可以做到，这样就达到了兼容。

例如某一个wrapper如下定义：

```css
.colwrapper{
  overflow:hidden; 
  zoom:1; 
  margin:5px auto;
}
```
③对于排版,我们用得最多的css描述可能就是float:left.有的时候我们需要在n栏的float div后面做一个统一的背景,譬如:

```html
<div id="page">
<div id="left">＜/div>
<div id="center">＜/div>
<div id="right">＜/div>
</div>
```
比如我们要将page的背景设置成蓝色,以达到所有三栏的背景颜色是蓝色的目的,但是我们会发现随着left center right的向下拉长,而page居然保存高度不变,问题来了css 制作,原因在于page不是float属性,而我们的page由于要居中,不能设置成 float,所以我们应该这样解决
```html
<div id="page">
<div id="bg" style=”"float:left;width:100%">
<div id="left">＜/div>
<div id="center">＜/div>
<div id="right">＜/div>
</div>
</div>
```

再嵌入一个float left而宽度是100%的DIV解决之道。

④万能float 闭合(非常重要!)

关于 clear CSS float 的原理可参见 How To Clear Floats Without Structural Markup,将以下代码加入Global CSS 中,给需要闭合的div加上 class="clearfix" 即可,屡试不爽.

```css
.clearfix:after {
 content:"."; 
 display:block;
 height:0;
 clear:both;
 visibility:hidden;
}
.clearfix {
 display:inline-block;
}
.clearfix {
 display:block;
}
```
或者这样设置：`.hackbox{ display:table; //将对象作为块元素级的表格显示}`

## 9．高度不能自适应

高度不能自适应是当内层对象的高度发生变化时外层高度不能自动进行调节，特别是当内层对象使用 `margin` 或 `paddign`时。

**例：**
```html
#box {
 background-color:#eee;
}
#box p {
 margin-top: 20px;
 margin-bottom: 20px;
 text-align:center;
}
<div id="box">
<p>对象中的内容＜/p>
</div>
```
*解决技巧*：在P对象上下各加2个空的div对象CSS代码：`.1{height:0px;overflow:hidden;}`或者为DIV加上border属性。

## 10 .div+css之IE6下为什么图片下有空隙产生

解决这个BUG的技巧也有很多,可以是改变html的排版,或者设置`img 为display:block` 或者设置`vertical-align` 属性为`vertical-align:top` 　

bottom middle text-bottom 都可以解决.

## 11.如何对齐文本与文本输入框

加上 `vertical-align:middle`;

```css
<style type="text/css">
<!--
input {
width:200px;
height:30px;
border:1px solid red;
vertical-align:middle;
}
-->
</style>
```

## 12.web标准中定义id与class区别吗

(1).web标准中是不容许重复ID的,比如 div id="aa" 不容许重复2次,而CSS class定义的是类,理论上可以无限重复, 这样需要多次引用的定义便可以使用他.

(2).属性的优先级问题

CSS ID的优先级要高于class,看上面的例子

(3).方便JS等客户端脚本,如果在页面中要对某个对象进行脚本操作,那么可以给他定义一个ID,否则只能利用遍历页面元素加上指定特定属性来找到它,这是相对浪费时间资源,远远不如一个ID来得简单。

## 13. LI中内容超过长度后以省略号显示的技巧

此技巧适用与IE与OP浏览器

```css
<style type="text/css">
<!--
li {
width:200px;
white-space:nowrap;
text-overflow:ellipsis;
-o-text-overflow:ellipsis;
overflow: hidden;
}

-->
</style>
```
## 14.为什么web标准中IE无法设置滚动条颜色了

解决办法是将body换成html
```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd>
<meta http-equiv="Content-Type" content="text/html; charset=gb2312" />

<style type="text/css">
<!--

html {
scrollbar-face-color:#f6f6f6;
scrollbar-highlight-color:#fff;
scrollbar-shadow-color:#eeeeee;
scrollbar-3dlight-color:#eeeeee;
scrollbar-arrow-color:#000;
scrollbar-track-color:#fff;
scrollbar-darkshadow-color:#fff;
}
-->
＜/style>
```
## 15.为什么无法定义1px左右高度的容器

IE6下这个问题是因为默认的行高造成的,解决的技巧也有很多,例如:

`overflow:hidden` 　 `zoom:0.08` 　 `line-height:1px`

## 16.怎么样才能让层显示在FLASH之上呢

解决的办法是给FLASH设置透明

`<param name="wmode" value="transparent" />`

## 17.怎样使一个层垂直居中于浏览器中

这里我们使用百分比绝对定位,与外补丁负值的技巧,负值的大小为其自身宽度高度除以二
```css
<style type="text/css">
<!--
div {
position:absolute;
top:50%;
left:50%;
margin:-100px 0 0 -100px;
width:200px;
height:200px;
border:1px solid red;
}
-->
</style>
``

## Firefox与IE的CSS兼容CSS HACK技巧

### 1. Div居中问题

div设置 margin-left, margin-right 为 auto 时已经居中，IE 不行，IE需要设定body居中，首先在父级元素定义text-algin: center;这个的意思就是在父级元素内的内容居中。

### 2.CSS 链接(a标签)的边框与背景

a链接加边框和背景色，需设置 display: block, 同时设置 float: left 保证不换行。参照 menubar, 给 a 和 menubar 设置高度是为了避免底边显示错位, 若不设 height, 可以在 menubar 中插入一个空格。

### 3.超链接访问过后hover样式就不出现的问题

被点击访问过的超链接样式不在具有hover和active了,很多人应该都遇到过这个问题,解决技巧是改变CSS属性的排列顺序: L-V-H-A

Code:
```css
<style type="text/css">
<!--
a:link {}
a:visited {}
a:hover {}
a:active {}
-->
</style>
```
### 1. 游标手指cursor

`cursor: pointer` 可以同时在 IE FF 中显示游标手指状， hand 仅 IE 可以

### 2.UL的padding与margin

ul标签在FF中默认是有padding值的,而在IE中只有margin默认有值,所以先定义 `ul{margin:0;padding:0;}`就能解决大部分问题

### 3. FORM标签

这个标签在IE中,将会自动margin一些边距,而在FF中margin则是0,因此,如果想显示一致,所以最好在css中指定margin和 padding,针对上面两个问题,我的css中一般首先都使用这样的样式`ul,form{margin:0;padding:0;}`给定义死了,所以后面就不会为这个头疼了.

### 4. BOX模型解释不一致问题

在FF和IE中的BOX模型解释不一致导致相差2px解决技巧：div{margin:30px!important;margin:28px;} 注意这两个margin的顺序一定不能写反， important这个属性IE不能识别，但别的浏览器可以识别。所以在IE下其实解释成这样：

`div{maring:30px;margin:28px}`重复定义的话按照最后一个来执行，所以不可以只写`margin:xx px!important;` `#box{ width:600px; //for ie6.0- w\idth:500px; //for ff+ie6.0}`
`#box{ width:600px!important //for ff width:600px; //for ff+ie6.0 width :500px; //for ie6.0-}`

### 5.属性选择器(这个不能算是兼容,是隐藏css的一个bug)

`p[id]{}div[id]{}`

这个对于IE6.0和IE6.0以下的版本都隐藏,FF和OPera作用.属性选择器和子选择器还是有区别的,子选择器的范围从形式来说缩小了,属性选择器的范围比较大,如p[id]中,所有p标签中有id的都是同样式的.

### 6.最狠的手段 - !important

如果实在没有办法解决一些细节问题,可以用这个技巧.FF对于”!important”会自动优先解析,然而IE则会忽略.如下
```css
.tabd1{
background:url(res/images/up/tab1.gif) no-repeat 0px 0px !important;
background:url(res/images/up/tab1.gif) no-repeat 1px 0px; }
```

值得注意的是，一定要将xxxx !important 这句放置在另一句之上。

### 7.IE,FF的默认值问题

或许你一直在抱怨为什么要专门为IE和FF写不同的CSS，为什么IE这样让人头疼，然后一边写css，一边咒骂那个可恶的M$ IE.其实对于css的标准支持方面，IE并没有我们想象的那么可恶，关键在于IE和FF的默认值不一样而已，掌握了这个技巧，你会发现写出兼容FF和 IE的css并不是那么困难，或许对于简单的css，你完全可以不用”!important”这个东西了。

我们都知道，浏览器在显示网页的时候，都会根据网页的css样式表来决定如何显示，但是我们在样式表中未必会将所有的元素都进行了具体的描述，当然也没有必要那么做，所以对于那些没有描述的属性，浏览器将采用内置默认的方式来进行显示，譬如文字，如果你没有在css中指定颜色，那么浏览器将采用黑色或者系统颜色来显示，div或者其他元素的背景，如果在css中没有被指定，浏览器则将其设置为白色或者透明，等等其他未定义的样式均如此。所以有很多东西出现 FF和IE显示不一样的根本原因在于它们的默认显示不一样，而这个默认样式该如何显示我知道在w3中有没有对应的标准来进行规定，因此对于这点也就别去怪罪IE了。

### 8.为什么FF下文本无法撑开容器的高度

标准浏览器中固定高度值的容器是不会象IE6里那样被撑开的,那我又想固定高度,又想能被撑开需要怎样设置呢？办法就是去掉height设置min- height:200px; 这里为了照顾不认识min-height的IE6 可以这样定义:
```css
{
height:auto!important;
height:200px;
min-height:200px;
}
```

### 9.FireFox下如何使连续长字段自动换行

众所周知IE中直接使用 `word-wrap:break-word` 就可以了, FF中我们使用JS插入
的技巧来解决

```html
<style type="text/css">
<!--
div {
width:300px;
word-wrap:break-word;
border:1px solid red;
}
-->
</style>
<div id="ff">aaaaaaaaaaaaaaaaaaaaaaaaaaaa＜/div>
```

```javascript
<scrīpt type="text/javascrīpt">

function toBreakWord(el, intLen){
var ōbj=document.getElementByIdx_x_xx_x_x(el);
var strContent=obj.innerHTML;
var strTemp="";
while(strContent.length>intLen){
strTemp+=strContent.substr(0,intLen)+"
";
strContent=strContent.substr(intLen,strContent.length);
}
strTemp+="
"+strContent;
obj.innerHTML=strTemp;
}
if(document.getElementByIdx_x_xx_x_x && !document.all) toBreakWord("ff", 37);

</scrīpt>
```

### 10.为什么IE6下容器的宽度和FF解释不同？

问题的差别在于容器的整体宽度有没有将边框（border）的宽度算在其内,这里IE6解释为200PX ,而FF则解释为220PX,那究竟是怎么导致的问题呢？大家把容器顶部的xml去掉就会发现原来问题出在这,顶部的申明触发了IE的qurks mode。
```html
<?xml version="1.0" encoding="gb2312"?>
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd>
<meta http-equiv="Content-Type" content="text/html; charset=gb2312" />
<style type="text/css">
<!--
div {
cursor:pointer;
width:200px;
height:200px;
border:10px solid red
}
-->
</style>
<div ōnclick="alert(this.offsetWidth)">让FireFox与IE兼容＜/div>
```
IE7.0对CSS的支持又有新问题，解决如下。

第一种，CSS HACK
```css
Bpx;
_height:20px;
```
注意顺序。

下面这样也属于div CSS HACK，不过没有上面这样简洁。
```css
#example { color: #333; }
* html #example { color: #666; }
*+html #example { color: #999; }
```
第二种，是使用IE专用的条件CSS注释
```html
<!--其他浏览器 -->
<link rel="stylesheet" type="text/css" href="css.css" />

<!--[if IE 7]>
<!-- 适合于IE7 -->
<link rel="stylesheet" type="text/css" href="ie7.css" />
<![endif]-->

<!--[if lte IE 6]>
<!-- 适合于IE6及一下 -->
<link rel="stylesheet" type="text/css" href="ie.css" />
<![endif]-->
```
第三种，css filter的办法，以下是从国外网站翻译过来的。

新建一个css样式如下：
```cs
#item {
width: 200px;
height: 200px;
background: red;
}
```
新建一个div,并使用前面定义的css的样式：
```html
<div id="item">some text here＜/div>
```
在body表现这里加入lang属性,中文为zh：
```html
<body lang="en">
```
现在对div元素再定义一个样式：
```css
*:lang(en) #item{
background:green !important;
}
```


  [1]: https://imgs.gnux.cn/usr/uploads/2016/02/3074511585.jpg