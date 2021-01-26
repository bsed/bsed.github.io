---
title: "JS jQuery分别获取选中的复选框值"
categories: [ "JS" ]
tags: [ "jquery","javascript","checkbox" ]
draft: false
slug: "jquery-js-to-obtain-the-selected-check-box-value"
date: "2012-11-13 09:40:00"
---

**html代码:**

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<style>

</style>
<title>JS获取复选框被选中的值</title>
</head>
<body>
<input type="checkbox" name="test" value="0" />0&nbsp;&nbsp;
<input type="checkbox" name="test" value="1" />1&nbsp;&nbsp;
<input type="checkbox" name="test" value="2" />2&nbsp;&nbsp;
<input type="checkbox" name="test" value="3" />3&nbsp;&nbsp;
<input type="checkbox" name="test" value="4" />4&nbsp;&nbsp;
<input type="checkbox" name="test" value="5" />5&nbsp;&nbsp;
<input type="checkbox" name="test" value="6" />6&nbsp;&nbsp;
<input type="checkbox" name="test" value="7" />7&nbsp;&nbsp;
<input type="button" onclick="chk()" value="提  交" />
</body>
</html>

```


<!--more-->


**js代码：**

```javascript
<script src="jquery.js"></script><!--这是载入jquery.js文件，如果不使用jquery可以去掉-->  
<script>  
function chk(){  
  var obj=document.getElementsByName('test');  //选择所有name="'test'"的对象，返回数组  
  //取到对象数组后，我们来循环检测它是不是被选中  
  var s='';  
  for(var i=0; i<obj.length; i++){  
    if(obj[i].checked) s+=obj[i].value+',';  //如果选中，将value添加到变量s中  
  }  
  //那么现在来检测s的值就知道选中的复选框的值了  
  alert(s==''?'你还没有选择任何内容！':s);  
}  
  
function jqchk(){  //jquery获取复选框值  
  var chk_value =[];  
  $('input[name="test"]:checked').each(function(){  
   chk_value.push($(this).val());  
  });  
  alert(chk_value.length==0 ?'你还没有选择任何内容！':chk_value);  
}  
</script>  
```