---
title: ThinkPHP U函数最经典的写法
date: 2012-06-20 17:47:00
updated: 2015-01-08 15:27:39
tags: 
- php
- clone
categories: 
- php

---
第一种:

    <ul class="Rtext">
         <volist name="list" id="vo">
                     <li>
                       <a href='{:U("Index/showContent/id/$vo[id]")}' title="{$vo.title}">
                      <span class="text">{$vo.title|substr_chinese=###,0,35}</span>{$vo.post_time|date='Y-m-d',###}</a>
                     </li>
         </volist>
    </ul>

单引号与双引号都不能错，必须一模一样。是不是值得收藏啊！ 

第二种（标准写法）：建议使用

    ('User/edit', array('id'=>$vo['id'])) 