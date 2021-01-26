---
title: "用Checkbox 黑客方式实现下拉列表框(Dropdown)"
categories: [ "Css" ]
tags: [ "css","dropdowns" ]
draft: false
slug: "using-checkbox-hacker-way-to-achieve-the-dropdown-list-box-dropdown"
date: "2015-09-06 19:59:00"
---

通过HTML元素和CSS选择器实现Dropdown 下拉列表框，不再以来jQuery.
下面是代码：

*html:*

    <div class="dropdown">
        <input type="checkbox" id="checkbox-toggle">
        <label for="checkbox-toggle">Click to Expand</label>
        <ul>
            <li><a href="#">Link One</a></li>
            <li><a href="#">Link Two</a></li>
            <li><a href="#">Link Three</a></li>
            <li><a href="#">Link Four</a></li>
        </ul>
    </div>


<!--more-->


*CSS:*

    body{
        text-align: center;
        font: 16px/1.5 sans-serif;
        padding-top: 40px;
        background-color: #ECEFF1;
    }
    
    .dropdown{
        position: relative;
        display: inline-block;
        font-size: 16px;
        color: #FFF;
    }
    
    /**
        Hide the checkbox itself. Checking and unchecking 
        it we will be done via the label element.
    */
    
    input[type=checkbox]{
        display: none;
    }
    
    
    /* Click to expand button */
    
    label{
        box-sizing: border-box;
        display: inline-block;
        width: 100%;
        background-color: #57A0D4;
        padding: 15px 20px;
    
        cursor: pointer;
        text-align: center;
        box-shadow: 0 1px 1px rgba(0, 0, 0, 0.2);
    
        -webkit-user-select: none;
        -moz-user-select: none;
        -ms-user-select: none;
        user-select: none;
    }
    
    
    /*  The ul will have display:none by default */
    
    ul{
        position: absolute;
        list-style: none;
        text-align: left;
        width: 100%;
        z-index: 1;
        margin:0;
        padding:0;
        box-shadow: 0px 1px 1px rgba(0, 0, 0, 0.2);
    
        display: none;
    }
    
    
    ul li{
        padding: 15px;
        background-color: #fff;
        color: #4FB9A7;
        margin-bottom: 1px;
        cursor: pointer;
    }
    
    ul li:hover{
        background-color: #4FB9A7;
        color: #FFF;
    }
    
    ul li a{
        color: inherit;
        text-decoration: none;
    }
    
    
    /**
    
        By using the Following-sibling selector (~),
        we can target elements positioned after our checkbox in the DOM tree.
    
        With the state pseudo selector (:checked),
        we can make changes depending on the state of the checkbox.
    
        Using this combination of selectors
        allows to change the color of the label
        and show the list of items
        only when the checkbox is checked.
    
    */
    
    input[type=checkbox]:checked ~ label {
        background-color: #3D88BD;
    }
    
    
    input[type=checkbox]:checked ~ ul {
        display: block;
    }


从HTML的结构来看，input 标签要在label标签和ul 标签的前面，　通过下面的代码就可以看到为什么要这么做了．

    <div class="dropdown">
        <input type="checkbox" id="checkbox_toggle">
        <label for="checkbox_toggle">Click to Expand</label>
        <ul>
            <li><a href="#">Link One</a></li>
            <li><a href="#">Link Two</a></li>
            <li><a href="#">Link Three</a></li>
            <li><a href="#">Link Four</a></li>
        </ul>
    </div>
