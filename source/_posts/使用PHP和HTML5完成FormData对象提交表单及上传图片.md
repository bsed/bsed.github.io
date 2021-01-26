---
title: "使用PHP和HTML5完成FormData对象提交表单及上传图片"
categories: [ "PHP" ]
tags: [ "phpmyadmin","html5","formdata" ]
draft: false
slug: "use-php-and-html5-to-complete-the-formdata-object-to-submit-the-form-and-upload-pictures"
date: "2015-09-02 17:37:00"
---

    FormData 对象，可以把form中所有表单元素的name与value组成一个queryString，提交到后台。在使用Ajax提交时，使用FormData对象可以减少拼接queryString的工作量。
    使用FormData对象

## 1.创建一个FormData空对象，然后使用Append方法添加Key/Value

    var formdata = new FormData();
    formdata.append('name','fdipzone');
    formdata.append('gender','male');

## 2.取得Form对象，作为参数传入到FormData对象

    <form name="form1" id="form1">
    <input type="text" name="name" value="fdipzone">
    <input type="text" name="gender" value="male">
    </form>
    var form = document.getElementById('form1');
    var formdata = new FormData(form);


<!--more-->


使用FormData提交表单及上传文件：

    <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.0 Transitional//EN">
    <html>
    <head>
     <meta http-equiv="content-type" content="text/html; charset=utf-8">
     <title> FormData Demo </title>
     <script src="//code.jquery.com/jquery-1.11.0.min.js"></script>
     <script type="text/javascript">
     <!--
     function fsubmit(){
     var data = new FormData($('#form1')[0]);
    $.ajax({
    url: 'server.php',
    type: 'POST',
    data: data,
    dataType: 'JSON',
    cache: false,
    processData: false,
    contentType: false
     }).done(function(ret){
     if(ret['isSuccess']){
     var result = '';
    result += 'name=' + ret['name'] + '<br>';
    result += 'gender=' + ret['gender'] + '<br>';
    result += '<img src="' + ret['photo'] + '" width="100">';
    $('#result').html(result);
     }else{
    alert('提交失敗');
     }
     });
     return false;
     }
     -->
     </script>
    </head>
    <body>
     <form name="form1" id="form1">
     <p>name:<input type="text" name="name" ></p>
     <p>gender:<input type="radio" name="gender" value="1">male <input type="radio" name="gender" value="2">female</p>
     <p>photo:<input type="file" name="photo" id="photo"></p>
     <p><input type="button" name="b1" value="submit" onclick="fsubmit()"></p>
     </form>
     <div id="result"></div>
    </body>
    </html>

*server.php*

    <?php
    $name = isset($_POST['name'])? $_POST['name'] : '';
    $gender = isset($_POST['gender'])? $_POST['gender'] : '';
    $filename = time().substr($_FILES['photo']['name'], strrpos($_FILES['photo']['name'],'.'));
    $response = array();
    if(move_uploaded_file($_FILES['photo']['tmp_name'], $filename)){
    $response['isSuccess'] = true;
    $response['name'] = $name;
    $response['gender'] = $gender;
    $response['photo'] = $filename;
    }else{
    $response['isSuccess'] = false;
    }
    echo json_encode($response);
    ?>

