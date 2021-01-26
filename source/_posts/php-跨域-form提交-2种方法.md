---
title: "php 跨域 form提交 2种方法"
categories: [ "PHP" ]
tags: [ "php","form" ]
draft: false
slug: "php-cross-domain-form-2-methods-are-presented"
date: "2015-10-13 20:55:00"
---

出于安全因素考虑，直接跨域访问是不允许的，下面介绍二种跨域的方法。

## 通过php curl

    function curlPost($url,$params)
    {
     $postData = '';
     foreach($params as $k => $v)
     {
     $postData .= $k . '='.$v.'&';
     }
     rtrim($postData, '&');
     $ch = curl_init();
     curl_setopt($ch,CURLOPT_URL,$url);
     curl_setopt($ch,CURLOPT_RETURNTRANSFER,true);
    
     curl_setopt($ch,CURLOPT_HEADER, false);
     curl_setopt($ch, CURLOPT_POST, count($postData));
     curl_setopt($ch, CURLOPT_POSTFIELDS, $postData); 
    


<!--more-->


     $output=curl_exec($ch);
    
     curl_close($ch);
     return $output;
    }
    
    echo curlPost("http://test.com",array('name'=>"tank"));

以前很多人用curl来抓，邮箱的通讯录，不过现在已经不可以了。哈哈。
## 利用jquery form，ajax提交

### 下载jquery.form.js

### js代码

    $('#testform').submit(function() {
     $(this).ajaxSubmit({
     type: 'post', // 提交方式 get/post
     dataType:"json",//数据类型
     url: 'your url', // 需要提交的 url
     success: function(data) { // data 保存提交后返回的数据，一般为 json 数据
     // 此处可对 data 作相关处理
     alert('提交成功！');
     }
     $(this).resetForm(); // 提交后重置表单
     });
     return false; // 阻止表单自动提交事件
    });

## 传统的ajax方法

### js代码

    $("#ajax").click(function(){
     $.ajax({
     type: "POST",
     url: "http://xx.xxx.com/test2.php",
     data: 'name=ajax',
     dataType:"json",
     success: function(data){
     $('#Result').text(data.name);
     }
     });
     });

*test2.php:*

    <?php  
    header("Access-Control-Allow-Origin:http://blog.yigrherb.com");    //允许blog.yigrherb.com提交访问  
    //header("Access-Control-Allow-Origin:*");    //允许任何访问  
    echo json_encode($_POST);  

### ajax jsonp

### js代码

    $("#jsonp").click(function(){  
     $.ajax({  
     url: 'http://xx.xxx.com/test1.php',  
     data: {name: 'jsonp'},  
     dataType: 'jsonp',  
     jsonp: 'callback',      //为服务端准备的参数  
     jsonpCallback: 'getdata',   //回调函数  
     success: function(){  
     alert("success");  
     }  
     });  
     });  
      
    function getdata(data){  
     $('#Result').text(data.name);  
    }  

### test1.php:

    <?php  
     if(isset($_GET['name']) && isset($_GET['callback']))   //callback根js端要对应，不然会报错的  
     {  
     echo $_GET['callback']. '(' . json_encode($_GET) . ');';   //格式固定的，为什么这样，不清楚  
     }  
    ?>  

## $.getJSON

    $("#getjson").click(function(){  
     $.getJSON('http://xx.xxx.com/test1.php?name=getjson&callback=?', function(data){  //没有回调函数，直接处理  
     $('#Result').text(data.name);  
     });  
    });  

## $.getScript

    $("#getscript").click(function(){  
     $.getScript('http://xx.xxx.com/test1.php?name=getscript&callback=getdata');  //回调函数根jsonp一样  
    }); 