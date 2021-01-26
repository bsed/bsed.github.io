---
title: "jquery将表单数据转化为json对象的插件 "
categories: [ "日常" ]
tags: [ "jquery","json" ]
draft: false
slug: "jquery-form-json"
date: "2014-08-28 09:53:00"
---

jQuery是在web应用中使用的脚本语言之一，因其具有轻量级,易学易用等特点，已广泛应用，其中的ajax封装简化了我们的应用，对其表单数据序列化用如下方法：

1.serialize()方法

　　格式：var data = $("form").serialize();

　　功能：将表单内容序列化成一个字符串。

　　这样在ajax提交表单数据时，就不用一一列举出每一个参数。只需将data参数设置为 $("form").serialize() 即可。


<!--more-->


2.serializeArray()方法

　　格式：var jsonData = $("form").serializeArray();

　　功能：将页面表单序列化成一个JSON结构的对象。注意不是JSON字符串。

　　比如，[{"name":"lihui"},{...}] 获取数据为 jsonData[0].name

3.$.param()方法，可以把json格式数据序列化成字符串形式

      var obj={a:1,b:2}

      var s=$.param(obj);

   会形成a=1&b=2的形式

    (function($){
        $.fn.serializeJson=function(){
            var serializeObj={};
            var array=this.serializeArray();
            var str=this.serialize();
            $(array).each(function(){
                if(serializeObj[this.name]){
                    if($.isArray(serializeObj[this.name])){
                        serializeObj[this.name].push(this.value);
                    }else{
                        serializeObj[this.name]=[serializeObj[this.name],this.value];
                    }
                }else{
                    serializeObj[this.name]=this.value;
                }
            });
            return serializeObj;
        };
    })(jQuery);

用法：

    $("#myForm").bind("submit",function(e){
            e.preventDefault();
            console.log($(this).serializeJson());
        });

测试用例：

    <form id="myForm" action="#">
        <input name="name"/>
        <input name="age"/>
        <select multiple="multiple" name="interest" size="2">
            <option value ="interest1">interest1</option>
            <option value ="interest2">interest2</option>
            <option value="interest3">interest3</option>
            <option value="interest4">interest4</option>
        </select>
        <input type="checkbox" name="vehicle" value="Bike" /> I have a bike
        <input type="checkbox" name="vehicle" value="Car" /> I have a car
        <input type="submit"/>
    </form>