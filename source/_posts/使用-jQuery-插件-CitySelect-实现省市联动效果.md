---
title: "使用 jQuery 插件 CitySelect 实现省市联动效果"
categories: [ "JS" ]
tags: [ "CitySelect","三级联动" ]
draft: false
slug: "use-the-jquery-plugin-cityselect-achieve-provinces-linkage-effect"
date: "2012-09-24 17:19:00"
---

在一些和会员相关的注册系统中，让用户输入省市信息是非常常见的行为，并且一般都是要求做到省市区联动下拉效果，今天就给大家推荐一个 jQuery 插件 CitySelect，通过 JSON 数据实现省市联动效果
CitySelect 使用


<!--more-->


首先在head中载入jquery库和cityselect插件。

    <script type="text/javascript" src="js/jquery.js"></script> 
    <script type="text/javascript" src="js/jquery.cityselect.js"></script>

接下来，我们在#city中，放置三个select，并且三个select分别设置class属性为：prov、city、dist，分别表示省、市、区（县）三个下拉框。注意如果只想实现省市二级联动，则去掉第三个dist的select即可。

    <div id="city"> 
        <select class="prov"></select>  
        <select class="city" disabled="disabled"></select> 
        <select class="dist" disabled="disabled"></select> 
    </div>

调用 cityselect 插件非常简单，直接调用：

    $("#city").citySelect();

自定义参数调用，设置默认省市区。

    $("#city").citySelect({  
        url:"js/city.min.js",  
        prov:"湖南", //省份 
        city:"长沙", //城市 
        dist:"岳麓区", //区县 
        nodata:"none" //当子集无数据时，隐藏select 
    });

下载：http://www.helloweba.com/down-188.html