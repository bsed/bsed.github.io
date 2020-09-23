---
title: 使用Emmet提高HTML和CSS开发效率
date: 2015-09-28 20:56:00
updated: 2016-06-02 11:32:40
tags: 
- java
- tomat
categories: 
- java

---
#Emmet微指南
Emmet前身叫ZenCoding,个人觉得还是ZenCoding更贴切。  

它是一系列代码快捷写法的集合，并不是真正意义上的工具，目标是让前端攻城狮更快的写出HTML标签与CSS。
输入一串精简指令，然后按下生成键(通常为tab键，也有CTRL+E/CMD+E的)，即可转换成一大批需要花很多时间才能写出的代码，成倍提升开发效率。
毫不夸张的讲，节约时间100%以上，用*健步如飞*来形容一点也不过分，非常令人兴奋吧。  

大部分现代编辑器都有插件支持Emmet，比如
* Aptana/Eclipse (跨平台) — Emmet for Eclipse
* TextMate (Mac) — external download
* Coda 1.6 and 2.x (Mac) — external download
* Espresso (Mac) — external download
* Komodo Edit/IDE (跨平台) — external download
* Notepad++ (Windows)
* PSPad (Windows)
* Jet Brains Webstorm, PHPStorm, IDEA等


<!--more-->


之所以叫【微指南】，是因为本文并不打算列出Emmet的全部用法，个人觉得只要掌握常用的就够了。  
本人使用Webstorm，内置Emmet功能，使用tab作为生成键，下面进入正题。

##HTML
###1.创建初始文档
```html
html:5
```
==>
```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>

</body>
</html>
```

###2.单个标签
```html
div
```
==>
```html
<div></div>
```

###3.子元素
```html
section>div>p
```
==>
```html
<section>
    <div>
        <p></p>
    </div>
</section>
```

###4.相邻元素
```html
section+div+p
```
==>
```html
<section></section>
<div></div>
<p></p>
```

###5.多个元素
```html
ul>li*3
```
==>
```html
<ul>
    <li></li>
    <li></li>
    <li></li>
</ul>
```

###6.快速添加类名、ID、属性、文本
```html
div.foo
```
==>
```html
<div class="foo"></div>
```

```html
section#head
```
==>
```html
<section id="head"></section>
```

```html
div[myattr=myvalue]
```
==>
```html
<div myattr="myvalue"></div>
```

```html
p{文本文本}
```
==>
```html
<p>文本文本</p>
```

###7.变量
```html
ul>li.item-$*5
```
==>
```html
<ul>
    <li class="item-1"></li>
    <li class="item-2"></li>
    <li class="item-3"></li>
    <li class="item-4"></li>
    <li class="item-5"></li>
</ul>
```
```html
ul>li#item$@-*3
```
==>
```html
<ul>
    <li id="item3"></li>
    <li id="item2"></li>
    <li id="item1"></li>
</ul>
```

```html
ul>li#item$@-6*3
```
==>
```html
<ul>
    <li id="item8"></li>
    <li id="item7"></li>
    <li id="item6"></li>
</ul>
```
###8.组合
```html
section.outer>div.inner#innerId>p{文本文本$}*4
```
==>
```html
<section class="outer">
    <div class="inner" id="innerId">
        <p>文本文本1</p>

        <p>文本文本2</p>

        <p>文本文本3</p>

        <p>文本文本4</p>
    </div>
</section>
```
```html
(section.outer>div.inner#innerId>p{文本文本$})*2
```
==>
```html
<section class="outer">
    <div class="inner">
        <p>文本文本1</p>
    </div>
</section>
<section class="outer">
    <div class="inner">
        <p>文本文本2</p>
    </div>
</section>
```
```html
section>(header+(ul>li.item$*3)+footer)
```
==>
```html
<section>
    <header></header>
    <ul>
        <li class="item1"></li>
        <li class="item2"></li>
        <li class="item3"></li>
    </ul>
    <footer></footer>
</section>
```



看了以上的HTML DEMO，你一定会发现，Emmet的指令写法很接近CSS选择器，相信你可以很快掌握！效率噌噌噌往上升！

##CSS
由于CSS的属性非常多，属性值就更多了，这里只举几个例子，我们大可以发挥想象力，多多尝试，Emmet会带给你更多惊喜。  
其实我平时写CSS也没怎么用Emmet，写了这篇微指南，我自己也涨姿势了科科~
另外，WebStorm在less中也支持Emmet奥~

###属性
w ==> width    
h ==> height    
p ==> padding    
m ==> margin    
bd ==> border   
bdc ==> border-color    
bdw ==> border-width  
bds ==> border-style  

pt ==> padding-top  
pr ==> padding-right  
pb ==> padding-bottom   
pl ==> padding-left  

margin和border同理  

c ==> color  
f ==> font  
fz ==> font-size  

fl ==> float  
cl ==> clear  

po ==> position  
l ==> left  
r ==> right  

o ==> opacity  
ov ==> overflow  

看到这里，你一定看出了规律。    
没错，就是CSS属性的首字母，如果是子属性，就追加子属性的首字母。如果有相同首字母的CSS属性，就通过第二个字母加以区分。    
每当你需要写一个CSS属性的时候，思考一下这个属性各个单词的首字母尝试一下吧。  
如果首字母还不足以区分，再试试尾字母~  

###属性值
w10 ==> width: 10px;  
w20r ==> width: 20rem;  
h30e ==> height: 30em;  
h40p ==> height: 40%;  

left和right同理  

p10 ==> padding: 10px;    
p10-20 ==> padding: 10px 20px;  
p10-20-30 ==> padding: 10px 20px 30px;  
p40-50p ==> padding: 40px 50%;   
p20p30r40e60 ==> padding: 20% 30rem 40em 60px;  
p10--20 ==> padding: 10px -20px;  

margin和border同理  

fll ==> float: left;  
flr ==> float: right;  
clb ==> clear: both;  
cll ==> float: left;  
clr ==> float: right;  

por ==> position: relative;  
poa ==> position: absolute;  
pof ==> position: fixed;  

ovh ==> overflow: hidden;  
ovv ==> overflow: visible;  

-tra  
==>
```css
-webkit-transform: ;
  -moz-transform: ;
  -ms-transform: ;
  -o-transform: ;
  transform: ;
```

然后可以五处同步编辑！    

-wm-tra  
==>  
```css
-webkit-transform: ;
  -moz-transform: ;
  transform: ;
```

! ==> !important  

规律是不是也很好找？    
小伙伴们赶紧行动起来吧！  

##总结
以上就是Emmet的常用写法，并没有全部列出，但掌握这些已经够用了。    
要查看全部缩写，请移步 官网 http://docs.emmet.io/
