---
title: "在表单元素下使用伪选择器(Pseudo Selectors)"
categories: [ "Css" ]
tags: [ "css3","chrome","pseudo" ]
draft: false
slug: "when-the-form-elements-using-pseudo-selectors"
date: "2015-09-07 09:39:00"
---

自从发明了CSS，web开发者都希望有一种方式将样式嵌入到浏览器中。例如一些简单的元素标签button和input标签，但对于复杂的元素不好操作，例如select 和range。在这篇文章中，我会告诉你如何使用它。

## 为什么伪选择器(Pseudo Selectors)?

没有标准的方式自定义内置浏览器元素的外观。 例如,搜索输入显示了一个小“x”，你可以按＂ｘ＂取消你的搜索。这是作为一个单独的单元内部实现的．但你不能与常规的CSS实现它。Ｗ3C已经注意到了,于是他们增加了伪选择器．例如：＂ｘ＂ 选择器可以通过
`input[type=search]::-webkit-search-cancel-button.`

有一个 [selectors](https://gist.github.com/webtobesocial/aefd6e25064c08e0cc9a) 的只是列表，在目前已知的选择器。所有的浏览器都提供了自己的元素选择器，但是比较糟糕的情况是，没有提供一个共同的标准，使这混乱的局面难得以解决。但是目前，我们必须针对每个浏览器单独使用他所支持的选择器。

如果你使用的是Chrome选择器，你可以更容易的找出该使用那个选择器，如果你使用的是Ｓhadoｗ Dom元素，你将在浏览器中看到这个元素的内部结构，下面提供了如何使用Chrome DevＴools 的方法．


<!--more-->
打开开发工具，Setting->,General->, ->Elements,->Show user agent shadow DOM。这是当你注入页面的时候，你将看到#shadow-root，当你在＃Shadow-root 中选择一个元素的时候，将看到用user agent 渲染后的样式，这正是你要寻找到的选择器．

看图:
图片丢失:>

注意了，下面的例子主要工作在Chrome和Safari。Firefox和IE不支持许多必要(自定义内置的元素)的伪选择器，所以他们给普通版本。

## 1. Checkboxes
例如用的比较多的checkbox 复选框元素标签，我们除了可以给它添加宽度，高度属性后，还可以给它添加什么属性呢，在Chrome和　Safari　浏览器中可以给他添加:before 和:after　伪元素属性，你可以做一些有趣的事情，对Check用CSS做一些简单的动画过度，

通过设置`-webkit-appearance `为　`none`,我们将阻止浏览器的默认样式，实例代码见下面：

*html:*

    <label>
        <input type="checkbox" /> Regular Checkbox
    </label>
    
    <label>
        <input type="checkbox" class="styled" />
        Styled Checkbox
    </label>

*css:*

    body {
        font: 13px sans-serif;
        color:#444;
    }
    
    label {
        padding: 10px;
        display: block;
    }
    
    input[type=checkbox].styled {
        -webkit-appearance: none;
        height: 1em;
        position: relative;
        background-color: #276FF4;
        width: 1em;
        border-radius: 3px;
        outline: none;
        font-size: 16px;
        /* try this: */
        /* font-size: 36px; */
        vertical-align: middle;
        box-shadow: 0 1px 1px rgba(0, 0, 0, 0.2);
    }
    
    input[type=checkbox].styled:active {
        box-shadow: 0 0 0.8em rgba(0, 0, 0, 0.2) inset;
    }
    
    input[type=checkbox].styled:before,
    input[type=checkbox].styled:after {
        content: '';
        background-color: inherit;
        position: absolute;
        transition: 0.1s background-color;
    }
    
    input[type=checkbox].styled:after {
        width: 0.19em;
        height: 0.65em;
        -webkit-transform: rotate(36deg) translate(0.52em, -0.16em);
    }
    
    input[type=checkbox].styled:before {
        width: 0.2em;
        height: 0.4em;
        -webkit-transform: rotate(-58deg) translate(-0.2em, 0.48em);
    }
    
    /* We use the checked selector to make the pseudo elements visible */
    
    input[type=checkbox].styled:checked:after,
    input[type=checkbox].styled:checked:before {
        background-color: #fff;
    }

## 2. Radio Buttons
我们可以使用相同的技术在Radio按钮。在这里，它是简单的，因为我们不需要做一个检查标CSS。这只能在Chrome和Safari。其他浏览器显示规则的radio按钮。

*html:*

    <label>
        <input type="radio" name="test" /> Regular Radio
    </label>
    
    <label>
        <input type="radio" class="styled" name="test" /> Styled Radio
    </label>

*css:*

    body {
        font: 13px sans-serif;
        color:#444;
    }
    
    label {
        padding: 10px;
        display: block;
    }
    
    input[type=radio].styled {
        -webkit-appearance: none;
        height: 1em;
        position: relative;
        background-color: #276FF4;
        width: 1em;
        border-radius: 50%;
        outline: none;
        font-size: 14px;
        box-shadow: 0 1px 1px rgba(0, 0, 0, 0.2);
    }
    
    input[type=radio].styled:checked:before {
        content: '';
        height: 0.4em;
        width: 0.4em;
        background-color: #FFF;
        position: absolute;
        border-radius: 50%;
        top: 0.3em;
        left: 0.3em;
    }

## 3.Select Elements
Selector元素不同于CSS样式,当我们制作向下的箭头，我们可以用背景图和一些简单的填充．你可以给Option Elements　设置字体和背景,没有其他的了．

*html:*

    <label>
        <select>
            <option>Cheeze</option>
            <option>Bacon</option>
            <option>Pasta</option>
            <option>Pizza</option>
            <option>Ice Cream</option>
        </select> Regular Select
    </label>
    
    <label>
        <select class="styled">
            <option>Cheeze</option>
            <option>Bacon</option>
            <option>Pasta</option>
            <option>Pizza</option>
            <option>Ice Cream</option>
        </select> Styled Select
    </label>

*css:*

    body {
        font: 13px sans-serif;
        color:#444;
    }
    
    label {
        padding: 10px;
        display: block;
    }
    
    select.styled {
        -webkit-appearance: none;
        -moz-appearance: none;
        -ms-appearance: none;
        appearance: none;
        border: 1px solid #aaa;
        padding: 2px 15px 2px 5px;
        background-image: url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABMAAAAICAQAAACxSAwfAAAAUklEQVQY02P4z0AMRGZGMaShwCisyhITmb8huMzfEhOxKvuvsGAh208Ik+3ngoX/FbBbClcIUcSAw21QhXxfIIrwKAMpfNsEUYRXGVCEFc6CQwBqq4CCCtU4VgAAAABJRU5ErkJggg==), linear-gradient(#EDEDED, #EDEDED 38%, #DEDEDE);
        background-position: right center;
        background-repeat: no-repeat;
        border-radius: 2px;
        box-shadow: 0 1px 1px rgba(0, 0, 0, 0.1);
        color: #333;
    }
    
    /* Required to disable the default IE styles */
    select.styled::-ms-expand {
        display: none;
    }
    
    select.styled:hover {
        border-color: #909090;
    }
    
    /* You have limited control on the drop down list. Uncomment this: */
    /* select.styled option{
        background-color:green;
        color:#fff;
        font-size:20px;
    } */

## 4. Range elements

range 是新出现的表单元素，每个浏览器支持的CSS 选择器的数量有限，我们可以使用浏览器所制定的部分元素，可以访问这个[链接](https://gist.github.com/webtobesocial/aefd6e25064c08e0cc9a#input-typerange)来查看什么可用.

*html:*

    <label>
        <input type="range" /> Regular Range
    </label>
    
    <label>
        <input type="range" class="styled" /> Styled Range
    </label>

*css:*

    body {
        font: 13px sans-serif;
        color:#444;
    }
    
    label {
        padding: 10px;
        display: block;
    }
    
    input[type=range].styled {
        -webkit-appearance: none;
        outline: 0;
        width: 300px;
        transition: 0.2s;
    }
    
    /* Chrome */
    
    input[type=range].styled::-webkit-slider-runnable-track {
        height: 2px;
        background: #9DA0A6;
        border: none;
        border-radius: 3px;
    }
    
    input[type=range].styled::-webkit-slider-thumb {
        -webkit-appearance: none;
        height: 16px;
        width: 16px;
        border-radius: 50%;
        background: #FFF;
        margin-top: -7px;
        vertical-align: middle;
        border: 5px solid #276FF4;
        transition: 0.15s;
    }
    
    input[type=range].styled::-webkit-slider-thumb:active {
        height: 20px;
        width: 20px;
        margin-top: -9px;
    }
    
    /* Firefox */
    
    input[type=range].styled::-moz-range-track {
        height: 2px;
        background: #9DA0A6;
        border: none;
        border-radius: 3px;
    }
    
    input[type=range].styled::-moz-range-progress {
        background: #276FF4;
    }
    
    input[type=range].styled::-moz-range-thumb {
        height: 16px;
        width: 16px;
        border-radius: 50%;
        background: #FFF;
        margin-top: -7px;
        vertical-align: middle;
        border: 5px solid #276FF4;
        transition: 0.15s;
        box-sizing: border-box;
    }
    
    input[type=range].styled::-moz-range-thumb:active {
        height: 20px;
        width: 20px;
        margin-top: -9px;
    }
    
    input[type=range].styled::-moz-focus-outer {
        border: 0;
    }

## 5. Progress Bars
进度条Progress Bars 给我提供了很好的注意，你可以用嵌套div 实现不同的效果．

*html:*

    <label>
        <progress value="22" max="100"></progress> Regular Progress
    </label>
    
    <label>
        <progress value="22" max="100" class="styled"></progress> Styled Progress
    </label>

*CSS:*

    body {
        font: 13px sans-serif;
        color:#444;
    }
    
    label {
        padding: 10px;
        display: block;
    }
    
    progress.styled {
        margin-top: 15px;
        width: 200px;
        height: 12px;
        background-color: #ddd;
        border: none;
    }
    
    /* Chrome */
    
    progress.styled::-webkit-progress-bar {
        background-color: #ddd;
    }
    
    progress.styled::-webkit-progress-value {
        background-color: #266FF4;
    }
    
    /* Firefox */
    
    progress.styled::-moz-progress-bar {
        background-color: #266FF4;
    }

## 总结

我们在开发浏览器应用的时候，如果你不需要考虑兼容问题的话，可以直接使用效果更好，当然了，如果你要兼容不同的浏览器的话，你可以使用javascipt和jQuery做进一步的处理，例如[Chosen](http://harvesthq.github.io/chosen/)