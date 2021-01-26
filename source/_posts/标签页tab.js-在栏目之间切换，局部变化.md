---
title: "标签页tab.js 在栏目之间切换，局部变化"
categories: [ "JS" ]
tags: [ "js","tab.js" ]
draft: false
slug: "tabjs-change-items"
date: "2014-08-28 22:16:00"
---

1.在使用bootstrap 中，我们会用到在栏目之间切换，来刷新页面的局部，可以使用下面的方法  

    <link rel="stylesheet" href="http://cdn.bootcss.com/twitter-bootstrap/3.0.3/css/bootstrap.min.css">


<!--more-->


    <script src="http://cdn.bootcss.com/jquery/1.10.2/jquery.min.js"></script>
    <script src="http://cdn.bootcss.com/twitter-bootstrap/3.0.3/js/bootstrap.min.js"></script>
    <div class="row">
        <div class="col-md-2">
            <!-- Nav tabs -->
            <ul class="nav nav-pills nav-stacked" id="tab-nav">
              <li class="active"><a href="#home" data-toggle="tab">Home</a></li>
              <li><a href="#profile" data-toggle="tab">Profile</a></li>
    
            </ul>
        </div>
        <div class="col-md-10">
            <!-- Tab panes -->
            <div class="tab-content" style="margin:20px;background-color:red;height:300px;">
              <div class="tab-pane" id="home">1111</div>
              <div class="tab-pane" id="profile">22222</div>
    
            </div>
        </div>
    <div>
    <script>
    $('#tab-nav a').click(function (e) {
      e.preventDefault() 
     $(this).tab('show') //使用div来控制页面的切换
    ;
    })
    </script>

2.注意  

    <ul class="nav nav-pills nav-stacked" id="tab-nav"></ul> <!--中的id-->
    <li><a href="#profile" data-toggle="tab">Profile</a></li> <!--中的data-toggle属性，herf中的值就是要显示的div的id属性的name-->

3.效果如图：
![2014-08-28\_222459.png][1]


  [1]: http://yamlimg01.b0.upaiyun.com/2014/08/697983894.png