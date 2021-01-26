---
title: "纯css3写滑出收缩菜单"
categories: [ "Css" ]
tags: [ "css3","menu" ]
draft: false
slug: "pure-css3-write-out-the-shrink-menu"
date: "2014-07-06 22:19:00"
---

这里是css慢慢看样式哦

    *{box-sizing: border-box;}
    html,body{overflow-x: hidden;}
    
    .sidebar {position: fixed; top: 0; bottom: 0; left: -14rem; width: 14rem; visibility: hidden; overflow-y: auto; font-family: "PT Sans", Helvetica, Arial, sans-serif; font-size: .875rem; color: rgba(255,255,255,.6); background-color: #ac4142; -webkit-transition: all .3s ease-in-out; transition: all .3s ease-in-out; }
  


<!--more-->


  
    
    #sidebar-checkbox:checked ~ .sidebar, #sidebar-checkbox:checked ~ .wrap, #sidebar-checkbox:checked ~ .sidebar-toggle {
    -webkit-transform: translateX(14rem);
    -ms-transform: translateX(14rem);
    transform: translateX(14rem);
    }
    
    #sidebar-checkbox:checked + .sidebar {z-index: 10;visibility: visible;}
    
    .wrap, .sidebar, .sidebar-toggle {
    -webkit-backface-visibility: hidden;
    -ms-backface-visibility: hidden;
    backface-visibility: hidden;
    }
    
    .wrap, .sidebar-toggle {-webkit-transition: -webkit-transform .3s ease-in-out;transition: transform .3s ease-in-out;}
    .sidebar-toggle {position: fixed; width: 2.25rem; } 
    .sidebar-toggle {
    position: absolute; top: 1rem; left: 1rem; display: block; width: 2.2rem; padding: .5rem .65rem; color: #505050; background-color: #fff; border-radius: 4px; cursor: pointer; }
    
    .sidebar-toggle:before {
    display: block;
    content: "";
    width: 100%;
    padding-bottom: .125rem;
    border-top: .375rem double;
    border-bottom: .125rem solid;
    -webkit-box-sizing: border-box;
    -moz-box-sizing: border-box;
    box-sizing: border-box;}
    .sidebar-toggle:active, #sidebar-checkbox:checked ~ .sidebar-toggle {background-color: #ac4142; color:#fff; }
    .sidebar-checkbox{display:none;}

## 这里全是HTML，注意input的位置很重要哦

    <input type="checkbox" class="sidebar-checkbox" id="sidebar-checkbox">
    <div class="sidebar" id="sidebar">这里是菜单</div>
    <div class="wrap">这里是内容</div>
    <label for="sidebar-checkbox" class="sidebar-toggle"></label>

嵌入js文件点击其他地方收缩回去，不然只能点击按钮哦

    (function(document) {
        var toggle = document.querySelector('.sidebar-toggle');
        var sidebar = document.querySelector('#sidebar');
        var checkbox = document.querySelector('#sidebar-checkbox');
    
        document.addEventListener('click', function(e) {
          var target = e.target;
    
          if(!checkbox.checked ||
             sidebar.contains(target) ||
             (target === checkbox || target === toggle)) return;
    
          checkbox.checked = false;
        }, false);
    })(document);