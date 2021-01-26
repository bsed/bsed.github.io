---
title: "jQuery实现的全选、反选和不选功能"
categories: [ "JS" ]
tags: [ "全选" ]
draft: false
slug: "jquery-implementation-and-select-inverse-selection-function-1"
date: "2011-03-10 23:21:00"
---

jQuery实现的全选、反选和不选功能 示例：

css:
<style>
.top_title{text-align:center;}
.demo{width:520px; margin:40px auto 0 auto; min-height:250px;}
ul li{line-height:30px; padding:4px 0; font-size:14px}
.btn{overflow: hidden;display:inline-block;*display:inline;padding:4px 20px 4px;font-size:14px;line-height:18px;*line-height:20px;color:#fff;text-align:center;vertical-align:middle;cursor:pointer;background-color:#5bb75b;border:1px solid #cccccc;border-color:#e6e6e6 #e6e6e6 #bfbfbf;border-bottom-color:#b3b3b3;-webkit-border-radius:4px;-moz-border-radius:4px;border-radius:4px; margin-left:2px}
</style>

jQuery:

<script>  
$(function () {
    //全选或全不选
    $("#all").click(function(){   
        if(this.checked){   
            $("#list :checkbox").attr("checked", true);  
        }else{   
            $("#list :checkbox").attr("checked", false);
        }   
     }); 
    //全选  
    $("#selectAll").click(function () {
         $("#list :checkbox,#all").attr("checked", true);  
    });  
    //全不选
    $("#unSelect").click(function () {  
         $("#list :checkbox,#all").attr("checked", false);  
    });  
    //反选 
    $("#reverse").click(function () { 
         $("#list :checkbox").each(function () {  
              $(this).attr("checked", !$(this).attr("checked"));  
         });
         allchk();
    });
    
    //设置全选复选框
    $("#list :checkbox").click(function(){
        allchk();
    });
}); 
function allchk(){
    var chknum = $("#list :checkbox").size();//选项总个数
    var chk = 0;
    $("#list :checkbox").each(function () {  
        if($(this).attr("checked")==true){
            chk++;
        }
    });
    if(chknum==chk){//全选
        $("#all").attr("checked",true);
    }else{//不全选
        $("#all").attr("checked",false);
    }
}
</script>
Html

<div class="demo">
    <ul id="list">  
        <li><label><input type="checkbox" value="1"> iSun's Blog</label></li>
        <li><label><input type="checkbox" value="1"> iSun's Blog</label></li>
    </ul>
    <input type="checkbox" id="all">
    <input type="button" value="全选" class="btn" id="selectAll">  
    <input type="button" value="全不选" class="btn" id="unSelect">
    <input type="button" value="反选" class="btn" id="reverse"> 
</div>