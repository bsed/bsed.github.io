---
title: js验证大全---有关正则表达式
date: 2011-09-08 14:09:00
updated: 2015-09-18 14:18:14
tags: 
- template
- golang
categories: 
- go

---
有关正则表达式
1.只能输入数字和英文的：

    <input onkeyup="value=value.replace(/[\W]/g,'') " onbeforepaste="clipboardData.setData('text',clipboardData.getData('text').replace(/[^\d]/g,''))" ID="Text1" NAME="Text1">

2.只能输入数字的：

    <input onkeyup="value=value.replace(/[^\d]/g,'') " onbeforepaste="clipboardData.setData('text',clipboardData.getData('text').replace(/[^\d]/g,''))" ID="Text2" NAME="Text2">

3.只能输入全角的：

    <input onkeyup="value=value.replace(/[^\uFF00-\uFFFF]/g,'')" onbeforepaste="clipboardData.setData('text',clipboardData.getData('text').replace(/[^\uFF00-\uFFFF]/g,''))" ID="Text3" NAME="Text3">


<!--more-->


4.只能输入汉字的：

    <input onkeyup="value=value.replace(/[^\u4E00-\u9FA5]/g,'')" onbeforepaste="clipboardData.setData('text',clipboardData.getData('text').replace(/[^\u4E00-\u9FA5]/g,''))" ID="Text4" NAME="Text4">

--------------------------------------------------------------------------------

验证非法字符，sql

    function vdf()
    {
    var i,j,name,value,message,length,type,a=vdf.arguments,cb_name;
    for (i=0; i<(a.length-2); i+=3)
    {
    if (a[i].indexOf('#')!=-1)
    {
    name=fob(a[i].substr(0,a[i].indexOf('#')));
    cb_name=fob(a[i].substr((a[i].indexOf('#')+1),a[i].length));
    }
    else
    {
    name=fob(a[i]); // 控件名称
    }
    message=a[i+1]; // 提示信息
    type=a[i+2]; // 类型
    if (type!="r_time")
    {
    value=name.value.replace(/ +/g, ""); // 控件值
    }
    else
    {
    value=name.value;
    }

}

--------------------------------------------------------------------------------

// ===============判断复选框是否选中================ //

    if (type=="r_cb")
    {
    e=document.forms(0).elements;
    var flag=false;
    for (i=0;i<e.length;i++)
    {
    if (e[i]!=cb_name)
    {
    if (e[i].checked==true)
    {
    flag=true;
    break;
    }
    }
    if (i==e.length-1)
    {
    break;
    }
    }
    if (flag==false)
    {
    alert(message+"!\n"); //为空时出现的提示
    return false;
    }
    }
    
    // ===============判断下拉框是否选择================ //
    if (type=="r_sl")
    {
    if (name.selected==false)
    {
    alert(message+"!\n"); //为空时出现的提示
    return false;
    }
    }
    
    // ===============不能为空的判断================ //
    if (type=="r")
    {
    if (value=="") // 判断是否为空
    {
    alert(message+"!\n"); //为空时出现的提示
    name.focus();
    name.select();
    return false;
    }
    }
    
    // ===============不能为空的判断,但不获得焦点================ //
    if (type=="o_r")
    {
    if (value=="") // 判断是否为空
    {
    alert(message+"!\n"); //为空时出现的提示
    return false;
    }
    }
    // ===============只能输入中文================ //
    if (type=="r_china")
    {
    if (value.search(/^[\u4e00-\u9fa5]+$/)==-1)
    {
    alert(message+"!\n"); // 判断不能为空
    name.focus();
    name.select();
    return false;
    }
    }
    
    // ===============不能为空,必须是数字或者字符判断================ //
    if (type=="r_num_char")
    {
    if (value=="")
    {
    alert(message+"!\n"); //为空时出现的提示
    name.focus();
    name.select();
    return false;
    }
    if (value.search(/^[0-9a-zA-Z]+$/)==-1)
    {
    alert(message+"!\n"); //为空时出现的提示
    name.focus();
    name.select();
    return false;
    }
    }
    
    // ===============可以为空，不为空时，填数字================ //
    if (type=="num")
    {
    if (value.search(/^[0-9]+$/)==-1 && value!="")
    {
    alert(message+"!\n"); // 判断不能为空
    name.focus();
    name.select();
    return false;
    }
    }
    
    // ===============不能为空,必须是数字判断================ //
    if (type=="r_num")
    {
    if (value=="")
    {
    alert(message+"!\n");
    name.focus();
    name.select();
    return false;
    }
    if (value.search(/^[0-9]+$/)==-1)
    {
    alert(message+"!\n"); // 判断不能为空
    name.focus();
    name.select();
    return false;
    }
    }
    
    // ===============必须输入小于n的数字================ //
    if (type.indexOf("r_num<")!=-1)
    {
    length=type.substring((type.indexOf('<')+1),type.length); // 获得rn<后面的数
    
    if (value=="") // 为空做的提示
    {
    alert(message+"!\n");
    name.focus();
    name.select();
    return false;
    }
    if (value.search(/^[0-9]+$/)==-1) // 不是数字做的提示
    {
    alert(message+"!\n");
    name.focus();
    name.select();
    return false;
    }
    if (value.length>length) // 限制数字长度做的限制
    {
    alert(message+"!\n");
    name.focus();
    name.select();
    return false;
    }
    }
    
    // ===============必须输入大于n的数字================ //
    if (type.indexOf("r_num>")!=-1)
    {
    length=type.substring((type.indexOf('>')+1),type.length); // 获得rn<后面的数
    if (value=="") // 为空做的提示
    {
    alert(message+"!\n");
    name.focus();
    name.select();
    return false;
    }
    if (value.search(/^[0-9]+$/)==-1) // 不是数字做的提示
    {
    alert(message+"!\n");
    name.focus();
    name.select();
    return false;
    }
    if (value.length<length) // 限制数字长度做的限制
    {
    alert(message+"!\n");
    name.focus();
    name.select();
    return false;
    }
    }
    
    // ===============必须输入a-b位之间的数字================ //
    if (type.indexOf("r_num#<>")!=-1)
    {
    length=type.substr((type.indexOf('>')+1),type.length);
    length=length.substr(0,length.lastIndexOf("-"));
    length1=type.substring((type.indexOf('-')+1),type.length) // 获得rn<后面的数
    if (value=="") // 为空做的提示
    {
    alert(message+"!\n");
    name.focus();
    name.select();
    return false;
    }
    if (value.search(/^[0-9]+$/)==-1) // 不是数字做的提示
    {
    alert(message+"!\n");
    name.focus();
    name.select();
    return false;
    }
    if (value.length<length || value.length>length1) // 限制数字长度做的限制
    {
    alert(message+"!\n");
    name.focus();
    name.select();
    return false;
    }
    }
    
    
    // ===============不能为空,必须是float类型================ //
    if (type=="r_float")
    {
    if (value=="")
    {
    alert(message+"!\n");
    name.focus();
    name.select();
    return false;
    }
    if (value.search(/^[0-9]+$/)!=-1 || value.search(/^([0-9]+)|([0-9]+\.[0-9]*)|([0-9]*\.[0-9]+)$/)!=-1)
    {
    return true;
    }
    else
    {
    alert(message+"!\n"); // 判断不能为空
    name.focus();
    name.select();
    return false;
    }
    }
    
    // ===============判断email,不一定输入================ //
    if (type.indexOf("email")!=-1)
    {
    if (name.value!="")
    {
    if (value.search(/^[_\.a-z0-9]+@[a-z0-9]+[\.][a-z0-9]{2,}$/i)==-1)
    {
    alert(message+"!\n");
    name.focus();
    name.select();
    return false;
    }
    }
    }
    
    // ===============判断email,一定输入================ //
    if (type.indexOf("r_email")!=-1)
    {
    if (name.value=="")
    {
    alert(message+"!\n");
    name.focus();
    name.select();
    return false;
    }
    if (value.search(/^[_\.a-z0-9]+@[a-z0-9]+[\.][a-z0-9]{2,}$/i)==-1)
    {
    alert(message+"!\n");
    name.focus();
    name.select();
    return false;
    }
    }
    
    // ===============判断日期,比如2000-12-20================ //
    if (type=="r_date")
    {
    flag=true;
    getdate=value;
    if (getdate.search(/^[0-9]{4}-(0[1-9]|[1-9]|1[0-2])-((0[1-9]|[1-9])|1[0-9]|2[0-9]|3[0-1])$/)==-1) // 判断输入格式时候正确
    {
    flag=false;
    }
    else
    {
    var year=getdate.substr(0,getdate.indexOf('-')) // 获得年
    // 下面操作获得月份
    var transition_month=getdate.substr(0,getdate.lastIndexOf('-'));
    var month=transition_month.substr(transition_month.lastIndexOf('-')+1,transition_month.length);
    if (month.indexOf('0')==0)
    {
    month=month.substr(1,month.length);
    }
    // 下面操作获得日期
    var day=getdate.substr(getdate.lastIndexOf('-')+1,getdate.length);
    if (day.indexOf('0')==0)
    {
    day=day.substr(1,day.length);
    }
    //alert(month);
    //alert(day)
    //return false;
    if ((month==4 || month==6 || month==9 || month==11) && (day>30)) // 4,6,9,11月份日期不能超过30
    {
    flag=false;
    }
    if (month==2) // 判断2月份
    {
    if (LeapYear(year))
    {
    if (day>29 || day<1){ flag=false; }
    }
    else
    {
    if (day>28 || day<1){flag=false; }
    }
    }
    else
    {
    flag=true;
    }
    }
    if (flag==false)
    {
    alert(message+"!\n"); //为空时出现的提示
    name.focus();
    name.select();
    return false;
    }
    }

--------------------------------------------------------------------------------

from:http://www.klstudio.com/post/33.html
功能说明:
表单的验证一直是网页设计者头痛的问题，表单验证类 Validator就是为解决这个问题而写的，旨在使设计者从纷繁复杂的表单验证中解放出来，把精力集中于网页的设计和功能上的改进上。
Validator是基于JavaScript技术的伪静态类和对象的自定义属性，可以对网页中的表单项输入进行相应的验证，允许同一页面中同时验证多个表单，熟悉接口之后也可以对特定的表单项甚至仅仅是某个字符串进行验证。因为是伪静态类，所以在调用时不需要实例化，直接以"类名+.语法+属性或方法名"来调用。此外，Validator还提供3种不同的错误提示模式，以满足不同的需要。
Validator目前可实现的验证类型有：
1.是否为空；
2.中文字符；
3.双字节字符
4.英文；
5.数字；
6.整数；
7.实数；
8.Email地址；
9.使用HTTP协议的网址；
10.电话号码；
11.货币；
12.手机号码；
13.邮政编码；
14.身份证号码；
15.QQ号码；
16.日期；
17.符合安全规则的密码；
18.某项的重复值；
19.两数的关系比较；
20.判断输入值是否在(n, m)区间；
21.输入字符长度限制(可按字节比较)；

22.对于具有相同名称的单选按钮的选中判断；
23.限制具有相同名称的多选按钮的选中数目；
24.自定义的正则表达式验证；
运行环境(客户端)：
在Windows Server 2003下用IE6.0+SP1和Mozilla Firefox 1.0测试通过；
在Lunix RedHat 9下的Netscape测试通过；

相关代码:

    <script>
    
    Validator = {
    Require : /.+/,
    Email : /^\w+([-+.]\w+)*@\w+([-.]\\w+)*\.\w+([-.]\w+)*$/,
    Phone : /^((\(\d{3}\))|(\d{3}\-))?(\(0\d{2,3}\)|0\d{2,3}-)?[1-9]\d{6,7}$/,
    Mobile : /^((\(\d{3}\))|(\d{3}\-))?13\d{9}$/,
    Url : /^http:\/\/[A-Za-z0-9]+\.[A-Za-z0-9]+[\/=\?%\-&_~`@[\]\’:+!]*([^<>\"\"])*$/,
    IdCard : /^\d{15}(\d{2}[A-Za-z0-9])?$/,
    Currency : /^\d+(\.\d+)?$/,
    Number : /^\d+$/,
    Zip : /^[1-9]\d{5}$/,
    QQ : /^[1-9]\d{4,8}$/,
    Integer : /^[-\+]?\d+$/,
    Double : /^[-\+]?\d+(\.\d+)?$/,
    English : /^[A-Za-z]+$/,
    Chinese : /^[\u0391-\uFFE5]+$/,
    UnSafe : /^(([A-Z]*|[a-z]*|\d*|[-_\~!@#\$%\^&\*\.\(\)\[\]\{\}<>\?\\\/\’\"]*)|.{0,5})$|\s/,
    IsSafe : function(str){return !this.UnSafe.test(str);},
    SafeString : "this.IsSafe(value)",
    Limit : "this.limit(value.length,getAttribute(’min’), getAttribute(’max’))",
    LimitB : "this.limit(this.LenB(value), getAttribute(’min’), getAttribute(’max’))",
    Date : "this.IsDate(value, getAttribute(’min’), getAttribute(’format’))",
    Repeat : "value == document.getElementsByName(getAttribute(’to’))[0].value",
    Range : "getAttribute(’min’) < value && value < getAttribute(’max’)",
    Compare : "this.compare(value,getAttribute(’operator’),getAttribute(’to’))",
    Custom : "this.Exec(value, getAttribute(’regexp’))",
    Group : "this.MustChecked(getAttribute(’name’), getAttribute(’min’), getAttribute(’max’))",
    ErrorItem : [document.forms[0]],
    ErrorMessage : ["以下原因导致提交失败：\t\t\t\t"],
    Validate : function(theForm, mode){
    var obj = theForm || event.srcElement;
    var count = obj.elements.length;
    this.ErrorMessage.length = 1;
    this.ErrorItem.length = 1;
    this.ErrorItem[0] = obj;
    for(var i=0;i<count;i++){
    with(obj.elements[i]){
    var _dataType = getAttribute("dataType");
    if(typeof(_dataType) == "object" || typeof(this[_dataType]) == "undefined") continue;
    this.ClearState(obj.elements[i]);
    if(getAttribute("require") == "false" && value == "") continue;
    switch(_dataType){
    case "Date" :
    case "Repeat" :
    case "Range" :
    case "Compare" :
    case "Custom" :
    case "Group" :
    case "Limit" :
    case "LimitB" :
    case "SafeString" :
    if(!eval_r(this[_dataType])) {
    this.AddError(i, getAttribute("msg"));
    }
    break;
    default :
    if(!this[_dataType].test(value)){
    this.AddError(i, getAttribute("msg"));
    }
    break;
    }
    }
    }
    if(this.ErrorMessage.length > 1){
    mode = mode || 1;
    var errCount = this.ErrorItem.length;
    switch(mode){
    case 2 :
    for(var i=1;i<errCount;i++)
    this.ErrorItem[i].style.color = "red";
    case 1 :
    alert(this.ErrorMessage.join("\n"));
    this.ErrorItem[1].focus();
    break;
    case 3 :
    for(var i=1;i<errCount;i++){
    try{
    var span = document.createElement("SPAN");
    span.id = "__ErrorMessagePanel";
    span.style.color = "red";
    this.ErrorItem[i].parentNode.appendChild(span);
    span.innerHTML = this.ErrorMessage[i].replace(/\d+:/,"*");
    }
    catch(e){alert(e.description);}
    }
    this.ErrorItem[1].focus();
    break;
    default :
    alert(this.ErrorMessage.join("\n"));
    break;
    }
    return false;
    }
    return true;
    },
    limit : function(len,min, max){
    min = min || 0;
    max = max || Number.MAX_VALUE;
    return min <= len && len <= max;
    },
    LenB : function(str){
    return str.replace(/[^\x00-\xff]/g,"**").length;
    },
    ClearState : function(elem){
    with(elem){
    if(style.color == "red")
    style.color = "";
    var lastNode = parentNode.childNodes[parentNode.childNodes.length-1];
    if(lastNode.id == "__ErrorMessagePanel")
    parentNode.removeChild(lastNode);
    }
    },
    AddError : function(index, str){
    this.ErrorItem[this.ErrorItem.length] = this.ErrorItem[0].elements[index];
    this.ErrorMessage[this.ErrorMessage.length] = this.ErrorMessage.length + ":" + str;
    },
    Exec : function(op, reg){
    return new RegExp(reg,"g").test(op);
    },
    compare : function(op1,operator,op2){
    switch (operator) {
    case "NotEqual":
    return (op1 != op2);
    case "GreaterThan":
    return (op1 > op2);
    case "GreaterThanEqual":
    return (op1 >= op2);
    case "LessThan":
    return (op1 < op2);
    case "LessThanEqual":
    return (op1 <= op2);
    default:
    return (op1 == op2);
    }
    },
    MustChecked : function(name, min, max){
    var groups = document.getElementsByName(name);
    var hasChecked = 0;
    min = min || 1;
    max = max || groups.length;
    for(var i=groups.length-1;i>=0;i--)
    if(groups[i].checked) hasChecked++;
    return min <= hasChecked && hasChecked <= max;
    },
    IsDate : function(op, formatString){
    formatString = formatString || "ymd";
    var m, year, month, day;
    switch(formatString){
    case "ymd" :
    m = op.match(new RegExp("^\\s*((\\d{4})|(\\d{2}))([-./])(\\d{1,2})\\4(\\d{1,2})\\s*$"));
    if(m == null ) return false;
    day = m[6];
    month = m[5]--;
    year = (m[2].length == 4) ? m[2] : GetFullYear(parseInt(m[3], 10));
    break;
    case "dmy" :
    m = op.match(new RegExp("^\\s*(\\d{1,2})([-./])(\\d{1,2})\\2((\\d{4})|(\\d{2}))\\s*$"));
    if(m == null ) return false;
    day = m[1];
    month = m[3]--;
    year = (m[5].length == 4) ? m[5] : GetFullYear(parseInt(m[6], 10));
    break;
    default :
    break;
    }
    var date = new Date(year, month, day);
    return (typeof(date) == "object" && year == date.getFullYear() && month == date.getMonth() && day == date.getDate());
    function GetFullYear(y){return ((y<30 ? "20" : "19") + y)|0;}
    }
    }
    </script>



//obj--验证的控件对象
//str--弹出的错误信息
//返回bool类型 验证对象是否没有填写内容
function isEmpty(obj,str)
{
if(obj.value.length==0)
{
   alert(str);
   obj.focus();
   return false;
}
return true;
}

// 验证对象obj中填写的email地址是否合法
function checkform(obj)
{
var email=obj.value;    
var pattern = /^([a-zA-Z0-9_-])+@([a-zA-Z0-9_-])+(\.[a-zA-Z0-9_-])+/;    
flag = pattern.test(email);    
if(!flag)    
{    
   alert("请核查您的Email输入地址是否正确");    
   obj.focus();    
   return false;
}   
return (true);
}

//sendto--收件人地址
//subject--邮件主题
//本函数对邮件发送内容进行验证
function checkUpPage(sever,sendto,subject)
{
    if(sever.value!=0)
    {
   if(isEmpty(sendto,"收件人不能为空！"))
   {
    if(checkform(sendto))
    {
     if(!isEmpty(subject,"邮件主题不能为空！"))
      return false;
     else
      return true;
    }
    else
    {
     return false;
    }
   }
   else
   {
    return false;
   }
    }
    else
    {
   alert("请选择发件箱");
   return false;
    }
}

在code中实用的话 在page_load()中加入如下语句

对象名称.Attributes.Add("onclick", "return checkUpPage(document.all.sender,document.all.receiver,document.all.subbject)");



