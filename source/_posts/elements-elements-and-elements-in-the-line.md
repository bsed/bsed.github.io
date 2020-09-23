---
title: 行内元素、块元素、空元素   之间的比较
date: 2011-12-29 15:17:00
updated: 2016-01-31 15:23:04
tags: 
- css
- html
- 兼容
categories: 
- css

---
## 行内元素的特点？

 1. 不可以设置宽高，但是可以与其他行内元素位于同一行
 2. 行内元素一般不可以包含块级元素。行内元素的高度由元素内部字体大小决定，宽度由内容长度决定
 3. 默认情况下，行内元素不会以新行开始，而块级元素会新起一行
 4. 行内元素也叫内联元素、内嵌元素、直进式元素

## 行内元素有哪些？
```html
b、big、small、tt
abbr、acronym、cite、code、dfn、em、kbd、strong、samp、var
a、bdo、br、img、map、object、q、script、span、sub、sup
button、input、label、select、textarea
```


<!--more-->


## 块级元素的特点？

 1. 独占一行（无法与其他元素显示在同一行里，除非强制修改元素的display属性）
 2. 可以设置宽高
 3. width默认100%

## 块级元素有哪些？
```html
address、blockquote、dd、div、dl、fieldset、form、h1~h6、hr、noscript、ol、ul、p、pre、table、tfoot
article、aside、audio、canvas、figcaption、footer、header、hgroup、output、section、video
```

## 空元素有哪些？

不能有内容的 HTML 元素被称为空元素。空元素是在开始标签中关闭的。常见的空元素有br、hr、img、input、link、meta，比较少见的有area、base、col、command、embed、keygen、param、source、track、wbr。

要想让元素既能在行内显示，又能设置宽高，可以设置其display属性为inline-block。

img、input默认display属性即为inline-block。

HTML5中不再使用块级、行内元素的分类方法，而采用流内容、措辞内容等类别。
