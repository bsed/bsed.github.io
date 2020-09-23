---
title: 如何调试CSS的跨浏览器样式bug
date: 2014-09-07 14:21:00
updated: 2014-09-07 14:30:33
tags: 
- javamodel
- memento
categories: 
- model

---
原文 http://www.stubbornella.org/content/2012/05/02/cross-browser-debugging-css/

作者为YAHOO前端工程师。

首先要做的是挑选一个好的浏览器。我的选择是Chrome，因为它拥有强大的调试工具。当我在Chrome上完成调试后，我会接着在Safari或者Firefox上调试。


<!--more-->


如果在这些“好的”浏览器上没有达到期望的效果，很有可能是代码本身违背了CSS规则。不要试图使用hack方法来解决在这些“好的”浏览器上出现的问题，而是应该找出问题的原因。通常我会检查以下可能的BUG出处：

* HTML代码解释 – 你是否忘记闭合一个标签？ 你是否用一个inline元素包住一个block元素？ 违背标准的代码可能在不同的浏览器上被解释呈现成不同的效果。
* 使用CSS lint工具检查CSS代码。留意那些检查出来的Errors。多数情况下，Errors比Warnings更容易引发跨浏览器差异。
* 忘记使用reset样式表，而是依靠于（不同的）浏览器默认的CSS样式。
* 浏览器支持性的差异。你是否使用了高级CSS3属性或者HTML5元素？查看浏览器支持性文档从而确保所有你的受众的浏览器都被涵盖。你需要设计“功能降级”来支持老式的浏览器。比如，把阴影边框降级成边框，或者把圆角降级成方块。
* 在不该有空格的地方加上了空格，margin可能因此呈现得诡异。
* 使用了绝对定位，可是没有设置垂直和水平偏移。这种情况下，绝对定位的元素将被呈现在跟position:static一样的位置上。但是，如果你尝试更改它的top，right，bottom或者left值，这个元素将马上“跳”到参照于它最近的相对定位的父元素的位置。
* 按照“不寻常”的方式组合了不同display方式的元素。比如，W3C标准并没有说当一个table-cell紧邻一个浮动元素时应该是怎样的layout。因此，这样写的代码并不是错误的，但可能会导致跨浏览器呈现不同效果的BUG。
* 空格是否影响了layout。你应该不想让排版样式依赖于空格。
* 小数点像素值会导致跨浏览器的不同效果。
最重要的需要记住的就是，W3C标准并没有定义错误的行为。因此，如果你没有按照规范写，那么可能会导致跨浏览器不同效果；如果你组合“奇怪的”属性（例如margin和inline element），那么也可能会导致跨浏览器不同效果bug。

####Display####

我认为书写CSS就像在选择一段旅程。你需要作出一些决定.比如你要首先选择使用不同display方式的元素：block，inline，inline-block和table。当你选择好以后，你可以使用一些具体的方法来改变其实际的显示。

块元素应该使用margin，padding，height和width。然而line-height不适用。

行内元素应该使用line-height，vertical align和空格符。然而margin，padding，height和width不适用。

首先，表格有垂直和水平排列方式。其次，如果你遗漏了表格中的某元素，整个表格可能会有诡异的显示。最后，margin不适用与表格的行和列，padding不适用与表格和表格的行。

####Positioning####

如果你选择使用块级元素，接下来你需要选择position方式：

* **Float** – 如果你使用了float，那么这个元素就变成了块级元素，而之前作用于该元素的vertical-align和line-height属性都将失效。
* **Absolute** – 相对于最近的相对定位的父节点来计算偏移量。当父节点和兄弟节点改变时，绝对定位的元素并不会导致reflow。这个特性有利于制作动画效果。但是，如果使用了绝对定位和动态更改内容将可能会导致显示问题，一个典型的例子是绝对定位的圆角框。
* **Static** – 默认的position方式。
* **Fixed** – 元素位置相对于浏览器窗口。不常使用的方式。
* **Relative** – 通常对于该元素样式不影响。只是其下属的绝对定位的子节点将相对于该节点计算偏移。
在这里我就不列举所有的display和position组合了。总之，有两件事情需要注意：

对于我选择的display和position方式，其他的属性（比如margin，line-height）是不是适合？
兄弟节点的position方式是不是契合？
比如，float，table-cell和行内元素组合在一起是否合适？浏览器将如何解释渲染？在W3C标准里有没有定义？如果没有，那么可能就有出现跨浏览器bug的风险。当然，这样的组合并不是不可以，但你要想清楚为什么要这样做，以及做好足够的跨浏览器测试。

####Internet Explorer####

当你解决了在“好的”浏览器上出现的问题后，现在应开始着手IE平台。我的建议是从你希望支持的最老版本的IE开始，因为很多老版本IE上的问题在新版本中延续出现。

就算对于IE，你也应该尝试找出问题而不是依赖于使用hack方法。盲目使用*和_的hack方法就像在一个返回错误值的函数中加入修正量（如下），而不是找出其中的算法性错误。

return result + 4;
当然，有时候在IE6和IE7里面使用hack是必要的。对于IE8，通常只在需要兼容CSS3的地方使用hack。通常情况下，在IE6/7里需要使用hack的地方有：

* hasLayout问题，使用zoom:1
* 相对定位导致元素消失
* 3px浮动BUG
* 被撑大的容器的浮动错误，可是经常被overflow:hidden碰巧的掩饰了。
还有一些不太常见的需要使用hack的情况，比如当两个浮动元素中间有comment代码时将会触发重复内容bug。对于只在IE中出现的css问题，我的建议是仔细描述你所看见的，并在google中搜索相应的解决方法。在你找到bug原因前，不要盲目使用hack掩饰它。IE自带的调试工具很糟糕，所以可能你需要给元素增加背景色来方便你查看页面上真实的排版。

####实现解决方案####

当你找到bug的原因并且知道解决方法后，你同时也应该知道如何在修改代码时不破坏已有的正常效果的代码。下面是我的建议：

* 依赖样式级联
* 使用针对特定浏览器的前缀
* 使用针对IE6/7的*和_
* 不要使用针对IE8的\9
* 知道什么时候该放弃针对IE的hack
* 不要对最新版本的Firefox，Chrome，Safari使用任何hack
#####1. 依赖样式级联#####

首先，在任何可能的情况下都尽量依靠样式级联。浏览器总是采取他们能够读懂的最后声明的样式。所以，你应该从针对老版本浏览器的样式开始书写，这样个浏览器就能读懂和使用它能读懂的最后的样式。例如：

    .foo {
    background-color: #ccc;    /* older browsers will use this */
        background-color: rgba(0,0,0,0.2);    /* browsers that understand rgba will use this */
    }

#####2. 使用针对特定浏览器的前缀#####

使用针对特定浏览器的前缀，尤其对于还未被广泛采用的属性适用。例如：

    .foo {
        background: #1e5799;     /* Old browsers */
        background: -moz-linear-gradient(top, #1e5799 0%, #2989d8 50%, #207cca 51%, #7db9e8 100%);     /* FF3.6+ */
        background: -webkit-gradient(linear, left top, left bottom, color-stop(0%,#1e5799), color-stop(50%,#2989d8), color-stop(51%,#207cca), color-stop(100%,#7db9e8));     /* Chrome,Safari4+ */
        background: -webkit-linear-gradient(top, #1e5799 0%,#2989d8 50%,#207cca 51%,#7db9e8 100%);     /* Chrome10+,Safari5.1+ */
        background: -o-linear-gradient(top, #1e5799 0%,#2989d8 50%,#207cca 51%,#7db9e8 100%);     /* Opera 11.10+ */
        background: -ms-linear-gradient(top, #1e5799 0%,#2989d8 50%,#207cca 51%,#7db9e8 100%);     /* IE10+ */
        background: linear-gradient(top, #1e5799 0%,#2989d8 50%,#207cca 51%,#7db9e8 100%);     /* W3C */
    }

注意，这套代码里有两个针对不同版本webkit的语法。前缀代码的顺序同样应该从针对老版本浏览器开始书写（参照第一条）。
如果有一个W3C标准定义的语法，你应该把它放在最后（例如上述代码最后一行）。这样随着浏览器开始支持这些新特性的标准语法，你的代码也能够稳健表现。

#####3. 使用针对IE6/7的*和_#####

对于旧版本IE特有的bug，使用*和_来弥补。比如：

    .clearfix {
        overflow: hidden;    /* new formatting context in better browsers */
        *overflow: visible;    /* protect IE7 and older from the overflow property */
        *zoom: 1;    /* give IE hasLayout, a new formatting context equivalent */
    }

所有的IE hack都针对于某版本和其之前的所有浏览器，比如：

_ 针对IE6和更早版本
* 针对IE7和更早版本
\9 针对IE8和更早版本 （注意，IE9在某些CSS属性上同样对于这个hack敏感）
所以，hack代码的顺序同样也应该从针对老版本浏览器开始书写（参照第一条）。

####4. 不要使用针对IE8的\9####

我从来不会使用\9来解决IE8里面出现的样式bug。我只会在弥补浏览器支持性上使用\9来做“降级”处理。比如我使用了box-shadow（在更先进的浏览器上正常），可是在IE8下很难看，因此我使用了\9来增加了一个新border。这种情况不能依靠样式级联(参照第一条)来处理，因为这是增加一个新样式，而不是修改一个已有的样式。

#####5. 知道什么时候该放弃针对IE的hack#####

不要试追求在IE中得到一模一样的效果。你是否愿意浪费额外的HTTP请求，繁杂的HTML/JS/CSS代码段为了实现在IE6-8中同样看到圆角框效果？对于我个人来说，我的答案是“不会”。

你应该知道什么时候放弃针对某功能的hack。例如，不要使用filter去模拟CSS3里的渐变效果，那样会导致性能问题和排版bug。最简单的办法是，压根不要寄希望你的网页在所有浏览器中都表现得一模一样。对于IE 6-8的用户，最好的办法就是给他们一个简单化的用户体验（注意，是简单化而不是残缺）。

下述糟糕的代码就是使用filter去模拟CSS3里的gradient：

.

    foo {
        filter: progid:DXImageTransform.Microsoft.gradient( startColorstr='#1e5799', endColorstr='#7db9e8',GradientType=0 );     /* IE6-9 */
    }

#####6. 不要对最新版本的Firefox，Chrome，Safari使用任何hack#####

对于Firefox，Chrome，Safari上的样式bug，最好的办法是仔细检查，很有可能这是因为你的代码违背了CSS的规则。