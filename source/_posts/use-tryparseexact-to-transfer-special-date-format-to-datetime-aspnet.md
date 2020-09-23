---
title: 用TryParseExact将特殊日期格式转回DateTime [ASP.NET]
date: 2018-06-26 10:47:00
updated: 2018-06-26 10:48:30
tags: 
- htaccess
categories: 
- linux

---
一般日期转换，我都会用`DateTime.TryParse`来转换，但如果不注意的话，会发生日期完全不对或失败的问题…

日期不对举凡像有些系统会使用05/07/2013这种格式，心裡想的是七月五号，但转换后会变成五月七号，另外一种情况就是自订格式转换的问题，今天收到客户送过来的日期格式如下"2018/06/13 09:30:45:4444"，如果用DateTime.TryParse就会回传false，这时就要用`DateTime.TryParseExact()`：


<!--more-->


```csharp
//特殊格式
string DateStr = "2018/06/13 09:30:45:4444";
DateTime _date;

//错误方式，返回False
if (DateTime.TryParse(DateStr, out _date))
{
    //TryParse转换成功
}

//正确方式
if(DateTime.TryParseExact(DateStr, "yyyy/MM/dd @ HH:mm:ss:ffff", null, System.Globalization.DateTimeStyles.None, out _date))
{
    //TryParseExact转换成功
}
```
