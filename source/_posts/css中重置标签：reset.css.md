---
title: "css中重置标签：reset.css"
categories: [ "Css" ]
tags: [ "reset" ]
draft: false
slug: "reset-label-in-css-resetcss"
date: "2014-07-08 22:24:00"
---

下面是重置的标签样式，清除不同浏览器的默认样式。


<!--more-->


    @charset "utf-8";
    html, body, div, span, applet, object, iframe,
    h1, h2, h3, h4, h5, h6, p, blockquote, pre,
    a, abbr, acronym, address, big, cite, code,
    del, dfn, em, font, img, ins, kbd, q, s, samp,
    small, strike, strong, sub, sup, tt, var,
    b, u, i, center,
    dl, dt, dd, ol, ul, li,
    fieldset, form, label, legend,
    table, caption, tbody, tfoot, thead, tr, th, td {
        margin: 0;
        padding: 0;
        border: 0;
        outline: 0;
        font-size: 100%;
        background: transparent;
    }
    table {
        border-collapse:collapse;
        border-spacing:0;
    }
    fieldset, img {    border:0; }
    address, caption, cite, code, dfn, em, strong, th, var {
        font-style:normal;
        font-weight:normal;
    }
    ol, ul { list-style:none; }
    caption, th { text-align:left; }
    h1, h2, h3, h4, h5, h6 {
        font-size:100%;
        font-weight:normal;
    }
    :focus { outline: 0;}
    a{ text-decoration:none;}
    a:hover img{ border:none;}
    
    /*清除浮动*/
    .clearfix:after {
        content: ".";
        display: block;
        height: 0;
        clear: both;
        visibility: hidden;
    }
    .clearfix {display: inline-block;}
    /* Hides from IE-mac \*/
    * html .clearfix { height: 1%;}
    .clearfix {display: block;}
    /* End hide from IE-mac */ 