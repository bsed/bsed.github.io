---
title: jQuery UI Dialog Widget每天只显示一次
date: 2015-09-06 11:56:00
updated: 2015-09-06 11:56:37
tags: 
- golang
categories: 
- go

---
因为要做一个会员提示信息，登陆会员页后需要获取提示信息，点击“不在显示后”，今天不在提示。

引用文件：
```
<link rel="stylesheet" type="text/css" href="http://code.jquery.com/ui/1.10.3/themes/smoothness/jquery-ui.css" charset="UTF-8">
<script type="text/javascript" src="http://code.jquery.com/jquery-1.9.1.js"></script>
<script type="text/javascript" src="http://code.jquery.com/ui/1.10.3/jquery-ui.js"></script>
```


<!--more-->


HTML代码：
```
<div id="dialog" title="标题" style="display:none;">
    <p>提示内容</p>
</div>
```

JavaScript代码：
```
<script type="text/javascript">
//<![CDATA[
jQuery(document).ready(function () {
    $('#dialog').dialog({
        autoOpen: false,
        width: 500,
        buttons: {
                "确定": function() { 
                    $(this).dialog("close"); 
                }, 
                "不再显示": function() { 
                    document.cookie="isshow=true";
                    $(this).dialog("close"); 
                } 
            }
    });
    showdiv();
});
 
function showdiv(){
    if(getck("isshow")  == "true"){
        if(getck("showdiv") == ""){
            var data = new Date();
            var timestr = data.getFullYear() + "-" + (Number(data.getMonth())+1) + "-" + data.getDate() + " " + data.getHours() + ":" + data.getMinutes() + ":" + data.getSeconds();
            document.cookie="showdiv=" + timestr;
        }else{
            var date_ = convertdate(getck("showdiv"));
            var data = new Date();
            var num = Number(data.getTime()) - Number(date_.getTime());
            if(num >= 24*3600000){//已经过期重新设置
                document.cookie="showdiv=";
                document.cookie="isshow=";
                $("#dialog").css('display','block');
                $('#dialog').dialog('open');
            }
        }
        return;
    }else{
        $("#dialog").css('display','block');
        $('#dialog').dialog('open');
    }
}
 
var acookie=document.cookie.split("; ");
function getck(sname){//获取单个cookies
    for(var i=0;i<acookie.length;i++){
        var arr=acookie[i].split("=");
        if(sname==arr[0]){
            if(arr.length>1) return unescape(arr[1]);
            else return "";
        }
    }
    return "";
}
 
function convertdate(datestr){
    var date_hidden = datestr;
    date_hidden = date_hidden.replace(":","-");
    date_hidden = date_hidden.replace(":","-");
    date_hidden = date_hidden.replace(" ","-");
    var date = new Date(Number(date_hidden.split("-")[0]),Number(date_hidden.split("-")[1])-1,Number(date_hidden.split("-")[2]),Number(date_hidden.split("-")[3]),Number(date_hidden.split("-")[4]),Number(date_hidden.split("-")[5]));
    return date;  
}
 
function clearcookie(){
    document.cookie="showdiv=";
    document.cookie="isshow=";//location.href=location.href;
}
//]]>
</script>
```