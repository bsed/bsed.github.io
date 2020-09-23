---
title: CSS3：RESET、标准注释、多屏幕尺寸兼容写法。
date: 2012-08-04 11:04:00
updated: 2015-08-04 11:04:52
tags: 
- Sublime-Text3
categories: 
- css

---
    /************************************************************************************
    RESET
    *************************************************************************************/


<!--more-->


    html, body, address, blockquote, div, dl, form, h1, h2, h3, h4, h5, h6, ol, p, pre, table, ul,
    dd, dt, li, tbody, td, tfoot, th, thead, tr, button, del, ins, map, object,
    a, abbr, acronym, b, bdo, big, br, cite, code, dfn, em, i, img, kbd, q, samp, small, span,
    strong, sub, sup, tt, var, legend, fieldset {
        margin: 0;
        padding: 0;
    }
    
    img, fieldset {
        border: 0;
    }
    
    /* set image max width to 100% */
    img {
        max-width: 100%;
        height: auto;
        width: auto\9; /* ie8 */
    }
    
    /* set html5 elements to block */
    article, aside, details, figcaption, figure, footer, header, hgroup, menu, nav, section { 
        display: block;
    }
    /************************************************************************************
    GENERAL STYLING
    *************************************************************************************/
    body {
        background: #0d1424 url(images/body-bg.jpg) no-repeat center top;
        font: .81em/150% Arial, Helvetica, sans-serif;
        color: #666;
    }
    a {
        color: #026acb;
        text-decoration: none;
        outline: none;
    }
    a:hover {
        text-decoration: underline;
    }
    p {
        margin: 0 0 1.2em;
        padding: 0;
    }
    
    /* list */
    ul, ol {
        margin: 1em 0 1.4em 24px;
        padding: 0;
        line-height: 140%;
    }
    li {
        margin: 0 0 .5em 0;
        padding: 0;
    }
    
    /* headings */
    h1, h2, h3, h4, h5, h6 {
        line-height: 1.4em;
        margin: 20px 0 .4em;
        color: #000;
    }
    h1 {
        font-size: 2em;
    }
    h2 {
        font-size: 1.6em;
    }
    h3 {
        font-size: 1.4em;
    }
    h4 {
        font-size: 1.2em;
    }
    h5 {
        font-size: 1.1em;
    }
    h6 {
        font-size: 1em;
    }
    
    /* reset webkit search input styles */
    input[type=search] {
        -webkit-appearance: none;
        outline: none;
    }
    input[type="search"]::-webkit-search-decoration, 
    input[type="search"]::-webkit-search-cancel-button {
        display: none;
    }
    /************************************************************************************
    STRUCTURE
    *************************************************************************************/
    #pagewrap {
        width: 980px;
        margin: 0 auto;
    }
    
    /************************************************************************************
    HEADER
    *************************************************************************************/
    
    /************************************************************************************
    MAIN NAVIGATION
    *************************************************************************************/
    
    /************************************************************************************
    CONTENT
    *************************************************************************************/
    
    /************************************************************************************
    SIDEBAR
    *************************************************************************************/
    
    /************************************************************************************
    FOOTER
    *************************************************************************************/
    
    /************************************************************************************
    CLEARFIX
    *************************************************************************************/
    .clearfix:after { visibility: hidden; display: block; font-size: 0; content: " "; clear: both; height: 0; }
    .clearfix { display: inline-block; }
    .clearfix { display: block; zoom: 1; }
    2、CSS3多屏幕尺寸兼容
    @media screen and (max-width:480px){}：表示可视区域最大宽不超过480px执行。
    适合多尺寸及屏幕旋转后的不同状态下的展现效果。
    
    /************************************************************************************
    smaller than 980
    *************************************************************************************/
    @media screen and (max-width: 980px) {
    
    }
    
    /************************************************************************************
    smaller than 650
    *************************************************************************************/
    @media screen and (max-width: 650px) {
    
    }
    
    /************************************************************************************
    smaller than 480
    *************************************************************************************/
    @media screen and (max-width: 480px) {
    
    }