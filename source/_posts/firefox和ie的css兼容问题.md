---
title: "firefox和ie的css兼容问题"
categories: [ "Css" ]
tags: [ "css","ie","firefox" ]
draft: false
slug: "firefox-ie-compatible"
date: "2014-09-06 09:51:00"
---

对于某些内容可变的层（比如用户评论），我们希望它有个最小的高度（比如30px），这样的话，即使内容只有一行字，也不会太难看；同时又希望在内容比较多的时候，层的高度能自动撑开，也就是要求height: auto。这时候就可以设置css的min-height属性。min-height在Firefox里有效，但IE无法识别。可以使用下面这个解决方案：


<!--more-->


    .div_class{
        min-height:30px;
        height:auto !important;
        height:30px;
    }

第一行设置 min-height:30px;对Firefox有效；第二行height:auto !important;也对Firefox有效，后面紧跟的“!important”是Firefox专用的一个标记，带有这个标记的设置具有最高优先级，之后的设置都无效。所以第三行的height:30px对Firefox无效了；同时，由于IE无法识别min-height和“! important”，所以只有第三行有效，由于IE默认就是高度自适应的，所以即使设置了30px的高度，只要内容很多，也会自动撑开，不需要设置 height:auto。最后，上述代码产生如下效果：

对于Firefox，等同于:

    .div_class{
        min-height:30px;
        height:auto;
    }

对于IE，等同于：

    .div_class{
        height:30px;
    }

“!important” 是个非常好用的东西，如果你写过几个月的跨浏览器的CSS代码，就很容易被Firefox和IE之间的差别而感到恼火。比如padding属性就是一个例子。
假设这样一个层：

    .div_name {
        width:100px;
        padding:10px;
    }

在IE里面，层的宽度是100px，四周的余空为10px；但是对于Firefox，层的宽度变成了100px+10px+10px=120px，对于宽度敏感的设计来说，整个就混乱了。怎么办呢？还是求助于“!important”吧。只要这样写就可以了：

    .div_name {
        width:80px !important;
        width:100px;
        padding:10px;
    }

因为80+10+10=100。正好让宽度 变成100px。
有时候，我们给一个层加上边框 ，在Firefox里面也会出现宽度增加的情况，比如：

    .div_name {
        width:100px;
        padding:10px;
        border:2px solid #ccc;
    }

上面这个层，在Firefox里面的实际宽度等于100+10+10+2+2=124px，因为边框也会增加宽度。怎么办呢，还是靠“!important”，这样写就可以了：

    .div_name {
        width:76px !important;
        width:100px;
        padding:10px;
        border:2px solid #ccc;
    }

写CSS经常要做这样的计算，还要写很多!important，苦了设计师们，什么时候IE和Firefox的CSS标准统一就好了。

