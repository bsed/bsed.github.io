---
title: 一些常用的正则表达式
date: 2011-09-14 13:08:00
updated: 2019-03-01 14:12:03
tags: 
- html5
- video
categories: 
- css

---
1、非负整数：^\d+$

2、正整数：^[0-9]*[1-9][0-9]*$

3、非正整数：^((-\d+)|(0+))$

4、负整数：^-[0-9]*[1-9][0-9]*$

5、整数：^-?\d+$

6、非负浮点数：^\d+(\.\d+)?$


区号+座机号码+分机号码：regexp="^(0[0-9]{2,3}/-)?([2-9][0-9]{6,7})+(/-[0-9]{1,4})?$"

手机(中国移动手机号码)：regexp="^((/(/d{3}/))|(/d{3}/-))?13[456789]/d{8}|15[89]/d{8}"

所有手机号码：regexp="^((/(/d{3}/))|(/d{3}/-))?13[0-9]/d{8}|15[89]/d{8}"(新添加了158,159两个号段)

((/d{11})|^((/d{7,8})|(/d{4}|/d{3})-(/d{7,8})|(/d{4}|/d{3})-(/d{7,8})-(/d{4}|/d{3}|/d{2}|/d{1})|(/d{7,8})-(/d{4}|/d{3}|/d{2}|/d{1}))$)

匹配格式：
11位手机号码
3-4位区号，7-8位直播号码，1－4位分机号
如：12345678901、1234-12345678-1234
 
1.用正则表达式限制只能输入全角字符： onkeyup="value=value.replace(/[^/uFF00-/uFFFF]/g,'')" onbeforepaste="clipboardData.setData('text',clipboardData.getData('text').replace(/[^/uFF00-/uFFFF]/g,''))" 

2.用正则表达式限制只能输入数字：onkeyup="value=value.replace(/[^/d]/g,'') "onbeforepaste="clipboardData.setData('text',clipboardData.getData('text').replace(/[^/d]/g,''))" 

3.用正则表达式限制只能输入数字和英文：onkeyup="value=value.replace(/[/W]/g,'') "onbeforepaste="clipboardData.setData('text',clipboardData.getData('text').replace(/[^/d]/g,''))" 

4.计算字符串的长度（一个双字节字符长度计2，ASCII字符计1） 

String.prototype.len=function(){return this.replace([^/x00-/xff]/g,"aa").length;} 

5.javascript中没有像vbscript那样的trim函数，我们就可以利用这个表达式来实现，如下： 

String.prototype.trim = function() 
{ 
return this.replace(/(^/s*)|(/s*$)/g, ""); 
} 

利用正则表达式分解和转换IP地址： 

6.下面是利用正则表达式匹配IP地址，并将IP地址转换成对应数值的Javascript程序： 

function IP2V(ip) 
{ 
re=/(/d+)/.(/d+)/.(/d+)/.(/d+)/g //匹配IP地址的正则表达式 
if(re.test(ip)) 
{ 
return RegExp.$1*Math.pow(255,3))+RegExp.$2*Math.pow(255,2))+RegExp.$3*255+RegExp.$4*1 
} 
else 
{ 
throw new Error("不是一个正确的IP地址!") 
} 
} 

不过上面的程序如果不用正则表达式，而直接用split函数来分解可能更简单，程序如下： 

var ip="10.100.20.168" 
ip=ip.split(".") 
alert("IP值是："+(ip[0]*255*255*255+ip[1]*255*255+ip[2]*255+ip[3]*1)) 
正则表达式用于字符串处理、表单验证等场合，实用高效。现将一些常用的表达式收集于此，以备不时之需。 


匹配中文字符的正则表达式： [/u4e00-/u9fa5] 
评注：匹配中文还真是个头疼的事，有了这个表达式就好办了 

匹配双字节字符(包括汉字在内)：[^/x00-/xff] 
评注：可以用来计算字符串的长度（一个双字节字符长度计2，ASCII字符计1） 

匹配空白行的正则表达式：/n/s*/r 
评注：可以用来删除空白行 

匹配HTML标记的正则表达式：<(/S*?)[^>]*>.*?<//1>|<.*? /> 
评注：网上流传的版本太糟糕，上面这个也仅仅能匹配部分，对于复杂的嵌套标记依旧无能为力 

匹配首尾空白字符的正则表达式：^/s*|/s*$ 
评注：可以用来删除行首行尾的空白字符(包括空格、制表符、换页符等等)，非常有用的表达式 

匹配Email地址的正则表达式：/w+([-+.]/w+)*@/w+([-.]/w+)*/./w+([-.]/w+)* 
评注：表单验证时很实用 

匹配网址URL的正则表达式：[a-zA-z]+://[^/s]* 
评注：网上流传的版本功能很有限，上面这个基本可以满足需求 

匹配帐号是否合法(字母开头，允许5-16字节，允许字母数字下划线)：^[a-zA-Z][a-zA-Z0-9_]{4,15}$ 
评注：表单验证时很实用 

匹配国内电话号码：/d{3}-/d{8}|/d{4}-/d{7} 
评注：匹配形式如 0511-4405222 或 021-87888822 

匹配腾讯QQ号：[1-9][0-9]{4,} 
评注：腾讯QQ号从10000开始 

匹配中国邮政编码：[1-9]/d{5}(?!/d) 
评注：中国邮政编码为6位数字 

匹配身份证：/d{15}|/d{18} 
评注：中国的身份证为15位或18位 

匹配ip地址：/d+/./d+/./d+/./d+ 
评注：提取ip地址时有用 

匹配特定数字： 
^[1-9]/d*$ //匹配正整数 
^-[1-9]/d*$ //匹配负整数 
^-?[1-9]/d*$ //匹配整数 
^[1-9]/d*|0$ //匹配非负整数（正整数 + 0） 
^-[1-9]/d*|0$ //匹配非正整数（负整数 + 0） 
^[1-9]/d*/./d*|0/./d*[1-9]/d*$ //匹配正浮点数 
^-([1-9]/d*/./d*|0/./d*[1-9]/d*)$ //匹配负浮点数 
^-?([1-9]/d*/./d*|0/./d*[1-9]/d*|0?/.0+|0)$ //匹配浮点数 
^[1-9]/d*/./d*|0/./d*[1-9]/d*|0?/.0+|0$ //匹配非负浮点数（正浮点数 + 0） 
^(-([1-9]/d*/./d*|0/./d*[1-9]/d*))|0?/.0+|0$ //匹配非正浮点数（负浮点数 + 0） 
评注：处理大量数据时有用，具体应用时注意修正 

匹配特定字符串： 
^[A-Za-z]+$ //匹配由26个英文字母组成的字符串 
^[A-Z]+$ //匹配由26个英文字母的大写组成的字符串 
^[a-z]+$ //匹配由26个英文字母的小写组成的字符串 
^[A-Za-z0-9]+$ //匹配由数字和26个英文字母组成的字符串 
^/w+$ //匹配由数字、26个英文字母或者下划线组成的字符串 
评注：最基本也是最常用的一些表达式 

匹配中文字符的正则表达式： [/u4e00-/u9fa5] 

匹配双字节字符(包括汉字在内)：[^/x00-/xff] 

匹配空行的正则表达式：/n[/s| ]*/r 

匹配HTML标记的正则表达式：/<(.*)>.*<///1>|<(.*) //>/ 

匹配首尾空格的正则表达式：(^/s*)|(/s*$) 

匹配Email地址的正则表达式：/w+([-+.]/w+)*@/w+([-.]/w+)*/./w+([-.]/w+)* 

匹配网址URL的正则表达式：http://([/w-]+/.)+[/w-]+(/[/w- ./?%&=]*)? 


^/d+$ //匹配非负整数（正整数 + 0） 
^[0-9]*[1-9][0-9]*$ //匹配正整数 
^((-/d+)|(0+))$ //匹配非正整数（负整数 + 0） 
^-[0-9]*[1-9][0-9]*$ //匹配负整数 
^-?/d+$ //匹配整数 
^/d+(/./d+)?$ //匹配非负浮点数（正浮点数 + 0） 
^(([0-9]+/.[0-9]*[1-9][0-9]*)|([0-9]*[1-9][0-9]*/.[0-9]+)|([0-9]*[1-9][0-9]*))$ //匹配正浮点数 
^((-/d+(/./d+)?)|(0+(/.0+)?))$ //匹配非正浮点数（负浮点数 + 0） 
^(-(([0-9]+/.[0-9]*[1-9][0-9]*)|([0-9]*[1-9][0-9]*/.[0-9]+)|([0-9]*[1-9][0-9]*)))$ //匹配负浮点数 
^(-?/d+)(/./d+)?$ //匹配浮点数 
^[A-Za-z]+$ //匹配由26个英文字母组成的字符串 
^[A-Z]+$ //匹配由26个英文字母的大写组成的字符串 
^[a-z]+$ //匹配由26个英文字母的小写组成的字符串 
^[A-Za-z0-9]+$ //匹配由数字和26个英文字母组成的字符串 
^/w+$ //匹配由数字、26个英文字母或者下划线组成的字符串 
^[/w-]+(/.[/w-]+)*@[/w-]+(/.[/w-]+)+$ 



"^/d+$"　　//非负整数（正整数 + 0） 
"^[0-9]*[1-9][0-9]*$"　　//正整数 
"^((-/d+)|(0+))$"　　//非正整数（负整数 + 0） 
"^-[0-9]*[1-9][0-9]*$"　　//负整数 
"^-?/d+$"　　　　//整数 
"^/d+(/./d+)?$"　　//非负浮点数（正浮点数 + 0） 
"^(([0-9]+/.[0-9]*[1-9][0-9]*)|([0-9]*[1-9][0-9]*/.[0-9]+)|([0-9]*[1-9][0-9]*))$"　　//正浮点数 
"^((-/d+(/./d+)?)|(0+(/.0+)?))$"　　//非正浮点数（负浮点数 + 0） 
"^(-(([0-9]+/.[0-9]*[1-9][0-9]*)|([0-9]*[1-9][0-9]*/.[0-9]+)|([0-9]*[1-9][0-9]*)))$"　　//负浮点
数 
"^(-?/d+)(/./d+)?$"　　//浮点数 
"^[A-Za-z]+$"　　//由26个英文字母组成的字符串 
"^[A-Z]+$"　　//由26个英文字母的大写组成的字符串 
"^[a-z]+$"　　//由26个英文字母的小写组成的字符串 
"^[A-Za-z0-9]+$"　　//由数字和26个英文字母组成的字符串 
"^/w+$"　　//由数字、26个英文字母或者下划线组成的字符串 
"^[/w-]+(/.[/w-]+)*@[/w-]+(/.[/w-]+)+$"　　　　//email地址 
"^[a-zA-z]+://(/w+(-/w+)*)(/.(/w+(-/w+)*))*(/?/S*)?$"　　//url 
/^13/d{9}$/gi手机号正则表达式
public static bool IsValidMobileNo(string MobileNo)
{
   const string regPattern = @"^(130|131|132|133|134|135|136|137|138|139)/d{8}$";
   return Regex.IsMatch(MobileNo, regPattern);
}
正则表达式--验证手机号码:13[0-9]{9}
实现手机号前带86或是+86的情况:^((/+86)|(86))?(13)/d{9}$
电话号码与手机号码同时验证:(^(/d{3,4}-)?/d{7,8})$|(13[0-9]{9}) 
提取信息中的网络链接:(h|H)(r|R)(e|E)(f|F) *= *('|")?(/w|//|//|/.)+('|"| *|>)?  
提取信息中的邮件地址:/w+([-+.]/w+)*@/w+([-.]/w+)*/./w+([-.]/w+)*  
提取信息中的图片链接:(s|S)(r|R)(c|C) *= *('|")?(/w|//|//|/.)+('|"| *|>)?
提取信息中的IP地址:(/d+)/.(/d+)/.(/d+)/.(/d+)    
提取信息中的中国手机号码:(86)*0*13/d{9}    
提取信息中的中国固定电话号码:(/(/d{3,4}/)|/d{3,4}-|/s)?/d{8}    
提取信息中的中国电话号码（包括移动和固定电话）:(/(/d{3,4}/)|/d{3,4}-|/s)?/d{7,14}    
提取信息中的中国邮政编码:[1-9]{1}(/d+){5}    
提取信息中的中国身份证号码:/d{18}|/d{15}    
提取信息中的整数：/d+    
提取信息中的浮点数（即小数）：(-?/d*)/.?/d+    
提取信息中的任何数字 ：(-?/d*)(/./d+)?  
提取信息中的中文字符串：[/u4e00-/u9fa5]*    
提取信息中的双字节字符串 (汉字)：[^/x00-/xff]*


<!--more-->


7、正浮点数：^((0-9)+\.[0-9]*[1-9][0-9]*)|([0-9]*[1-9][0-9]*\.[0-9]+)|([0-9]*[1-9][0-9]*))$

8、非正浮点数：^((-\d+\.\d+)?)|(0+(\.0+)?))$

9、负浮点数：^(-((正浮点数正则式)))$

10、英文字符串：^[A-Za-z]+$

11、英文大写串：^[A-Z]+$

12、英文小写串：^[a-z]+$

13、英文字符数字串：^[A-Za-z0-9]+$

14、英数字加下划线串：^\w+$

15、E-mail地址：^[\w-]+(\.[\w-]+)*@[\w-]+(\.[\w-]+)+$

16、URL：^[a-zA-Z]+://(\w+(-\w+)*)(\.(\w+(-\w+)*))*(\?\s*)?$
或：^http:\/\/[A-Za-z0-9]+\.[A-Za-z0-9]+[\/=\?%\-&_~`@[\]\':+!]*([^<>\"\"])*$

17、邮政编码：^[1-9]\d{5}$

18、中文：^[\u0391-\uFFE5]+$

19、电话号码：^((\(\d{2,3}\))|(\d{3}\-))?(\(0\d{2,3}\)|0\d{2,3}-)?[1-9]\d{6,7}(\-\d{1,4})?$

20、手机号码：^((\(\d{2,3}\))|(\d{3}\-))?13\d{9}$

21、双字节字符(包括汉字在内)：^\x00-\xff

22、匹配首尾空格：(^\s*)|(\s*$)（像vbscript那样的trim函数）

23、匹配HTML标记：<(.*)>.*<\/\1>|<(.*) \/>

24、匹配空行：\n[\s| ]*\r

25、提取信息中的网络链接：(h|H)(r|R)(e|E)(f|F) *= *('|")?(\w|\\|\/|\.)+('|"| *|>)?

26、提取信息中的邮件地址：\w+([-+.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*

27、提取信息中的图片链接：(s|S)(r|R)(c|C) *= *('|")?(\w|\\|\/|\.)+('|"| *|>)?

28、提取信息中的IP地址：(\d+)\.(\d+)\.(\d+)\.(\d+)

29、提取信息中的中国手机号码：(86)*0*13\d{9}

30、提取信息中的中国固定电话号码：(\(\d{3,4}\)|\d{3,4}-|\s)?\d{8}

31、提取信息中的中国电话号码（包括移动和固定电话）：(\(\d{3,4}\)|\d{3,4}-|\s)?\d{7,14}

32、提取信息中的中国邮政编码：[1-9]{1}(\d+){5}

33、提取信息中的浮点数（即小数）：(-?\d*)\.?\d+

34、提取信息中的任何数字 ：(-?\d*)(\.\d+)?

35、IP：(\d+)\.(\d+)\.(\d+)\.(\d+)

36、电话区号：/^0\d{2,3}$/

37、腾讯QQ号：^[1-9]*[1-9][0-9]*$

38、帐号(字母开头，允许5-16字节，允许字母数字下划线)：^[a-zA-Z][a-zA-Z0-9_]{4,15}$

39、中文、英文、数字及下划线：^[\u4e00-\u9fa5_a-zA-Z0-9]+$

匹配中文字符的正则表达式： [\u4e00-\u9fa5]

匹配双字节字符(包括汉字在内)：[^\x00-\xff]

匹配空行的正则表达式：\n[\s| ]*\r

匹配HTML标记的正则表达式：/<(.*)>.*<\/\1>|<(.*) \/>/

匹配首尾空格的正则表达式：(^\s*)|(\s*$)

匹配IP地址的正则表达式：/(\d+)\.(\d+)\.(\d+)\.(\d+)/g //

匹配Email地址的正则表达式：\w+([-+.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*

匹配网址URL的正则表达式：http://([\w-]+\.)+[\w-]+(/[\w- ./?%&=]*)?

sql语句：^(select|drop|delete|create|update|insert).*$


c# 
string Mobile = @"^1((3[0-9]|4[57]|5[0-35-9]|7[0678]|8[0-9])\d{8}$)";//手机
string Telephone = @"^(0[0-9]{2,3}\-)?([2-9][0-9]{6,7})+(\-[0-9]{1,4})?$";//电话
string MT = @"((^1((3[0-9]|4[57]|5[0-35-9]|7[0678]|8[0-9])\d{8}$))|(^(0[0-9]{2,3}\-)?([2-9][0-9]{6,7})+(\-[0-9]{1,4})?$))";//手机+电话
string CM = @"^1(3[4-9]|4[7]|5[0-27-9]|7[08]|8[2-478])\\d{8}$";//移动
string CU = @"^1(3[0-2]|4[5]|5[256]|7[016]|8[56])\\d{8}$";//联通
string CT = @"^1(3[34]|53|7[07]|8[019])\\d{8}$";//电信
string EM = @"([a-zA-Z0-9_\.\-])+\@(([a-zA-Z0-9\-])+\.)+([a-zA-Z0-9]{2,5})+";//邮箱



C# 校验帮助类-正则表达式

一、简介

很多时候我们都需要用到一些验证的方法，有时候需要用正则表达式校验数据时，往往需要到网上找很久，结果找到的还不是很符合自己想要的。所以我把自己整理的校验帮助类分享处理，在这里分享一下，给自己留了个底，也给各位需要的朋友们做个参考
二、校验类片段
2.1 邮箱验证

#region IsEmail(是否邮箱)
/// <summary>
/// 是否邮箱
/// </summary>
/// <param name="value">邮箱地址</param>
/// <param name="isRestrict">是否按严格模式验证</param>
/// <returns></returns>
public static bool IsEmail(string value, bool isRestrict=false)
{
    if (value.IsEmpty())
    {
        return false;
    }
    string pattern = isRestrict
        ? @"^(?("")("".+?""@)|(([0-9a-zA-Z]((\.(?!\.))|[-!#\$%&'\*\+/=\?\^`\{\}\|~\w])*)(?<=[0-9a-zA-Z])@))(?(\[)(\[(\d{1,3}\.){3}\d{1,3}\])|(([0-9a-zA-Z][-\w]*[0-9a-zA-Z]\.)+[a-zA-Z]{2,6}))$"
        : @"^[\w-]+(\.[\w-]+)*@[\w-]+(\.[\w-]+)+$";
        
    return value.IsMatch(pattern, RegexOptions.IgnoreCase);
}

/// <summary>
/// 是否存在邮箱
/// </summary>
/// <param name="value">值</param>
/// <param name="isRestrict">是否按严格模式验证</param>
/// <returns></returns>
public static bool HasEmail(string value, bool isRestrict = false)
{
    if (value.IsEmpty())
    {
        return false;
    }
    string pattern = isRestrict
        ? @"^(?("")("".+?""@)|(([0-9a-zA-Z]((\.(?!\.))|[-!#\$%&'\*\+/=\?\^`\{\}\|~\w])*)(?<=[0-9a-zA-Z])@))(?(\[)(\[(\d{1,3}\.){3}\d{1,3}\])|(([0-9a-zA-Z][-\w]*[0-9a-zA-Z]\.)+[a-zA-Z]{2,6}))$"
        : @"^[\w-]+(\.[\w-]+)*@[\w-]+(\.[\w-]+)+$";
    return value.IsMatch(pattern, RegexOptions.IgnoreCase);
}
#endregion

2.2 手机号码验证

#region IsPhoneNumber(是否合法的手机号码)
/// <summary>
/// 是否合法的手机号码
/// </summary>
/// <param name="value">手机号码</param>
/// <returns></returns>
public static bool IsPhoneNumber(string value)
{
    if (value.IsEmpty())
    {
        return false;
    }
    return value.IsMatch(@"^(0|86|17951)?(13[0-9]|15[012356789]|18[0-9]|14[57]|17[678])[0-9]{8}$");
}
#endregion

#region IsMobileNumber(是否手机号码)
/// <summary>
/// 是否手机号码
/// </summary>
/// <param name="value">手机号码</param>
/// <param name="isRestrict">是否按严格模式验证</param>
/// <returns></returns>
public static bool IsMobileNumberSimple(string value, bool isRestrict = false)
{
    if (value.IsEmpty())
    {
        return false;
    }
    string pattern=isRestrict ? @"^[1][3-8]\d{9}$" : @"^[1]\d{10}$";
    return value.IsMatch(pattern);
}
/// <summary>
/// 是否手机号码
/// </summary>
/// <param name="value">手机号码</param>
/// <returns></returns>
public static bool IsMobileNumber(string value)
{
    if (value.IsEmpty())
    {
        return false;
    }
    value = value.Trim().Replace("^", "").Replace("$", "");
    /**
     * 手机号码: 
     * 13[0-9], 14[5,7], 15[0, 1, 2, 3, 5, 6, 7, 8, 9], 17[6, 7, 8], 18[0-9], 170[0-9]
     * 移动号段: 134,135,136,137,138,139,150,151,152,157,158,159,182,183,184,187,188,147,178,1705
     * 联通号段: 130,131,132,155,156,185,186,145,176,1709
     * 电信号段: 133,153,180,181,189,177,1700
     */
    return value.IsMatch(@"^1(3[0-9]|4[57]|5[0-35-9]|8[0-9]|70)\d{8}$");
}

/// <summary>
/// 是否存在手机号码
/// </summary>
/// <param name="value">值</param>
/// <param name="isRestrict">是否按严格模式验证</param>
/// <returns></returns>
public static bool HasMobileNumberSimple(string value, bool isRestrict = false)
{
    if (value.IsEmpty())
    {
        return false;
    }
    string pattern = isRestrict ? @"[1][3-8]\d{9}" : @"[1]\d{10}";
    return value.IsMatch(pattern);
}
#endregion

#region IsChinaMobilePhone(是否中国移动号码)
/// <summary>
/// 是否中国移动号码
/// </summary>
/// <param name="value">手机号码</param>
/// <returns></returns>
public static bool IsChinaMobilePhone(string value)
{
    if (value.IsEmpty())
    {
        return false;
    }
    /**
     * 中国移动：China Mobile
     * 134,135,136,137,138,139,150,151,152,157,158,159,182,183,184,187,188,147,178,1705
     */
    return value.IsMatch(@"(^1(3[4-9]|4[7]|5[0-27-9]|7[8]|8[2-478])\d{8}$)|(^1705\d{7}$)");
}
#endregion

#region IsChinaUnicomPhone(是否中国联通号码)
/// <summary>
/// 是否中国联通号码
/// </summary>
/// <param name="value">手机号码</param>
/// <returns></returns>
public static bool IsChinaUnicomPhone(string value)
{
    if (value.IsEmpty())
    {
        return false;
    }
    /**
     * 中国联通：China Unicom
     * 130,131,132,155,156,185,186,145,176,1709
     */
    return value.IsMatch(@"(^1(3[0-2]|4[5]|5[56]|7[6]|8[56])\d{8}$)|(^1709\d{7}$)");
}
#endregion

#region IsChinaTelecomPhone(是否中国电信号码)
/// <summary>
/// 是否中国电信号码
/// </summary>
/// <param name="value">手机号码</param>
/// <returns></returns>
public static bool IsChinaTelecomPhone(string value)
{
    if (value.IsEmpty())
    {
        return false;
    }
    /**
     * 中国电信：China Telecom
     * 133,153,180,181,189,177,1700
     */
    return value.IsMatch(@"(^1(33|53|77|8[019])\d{8}$)|(^1700\d{7}$)");
}
#endregion

2.3 身份证验证

#region IsIdCard(是否身份证号码)
/// <summary>
/// 是否身份证号码
/// </summary>
/// <param name="value">身份证</param>
/// <returns></returns>
public static bool IsIdCard(string value)
{
    if (value.IsEmpty())
    {
        return false;
    }
    if (value.Length == 15)
    {
        return value.IsMatch(@"^[1-9]\d{7}((0\d)|(1[0-2]))(([0|1|2]\d)|3[0-1])\d{3}$");
    }
    return value.Length == 0x12 &&
           value.IsMatch(@"^[1-9]\d{5}[1-9]\d{3}((0\d)|(1[0-2]))(([0|1|2]\d)|3[0-1])((\d{4})|\d{3}[Xx])$",
               RegexOptions.IgnoreCase);
}
#endregion

2.4 Base64编码验证

#region IsBase64String(是否Base64编码)
/// <summary>
/// 是否Base64编码
/// </summary>
/// <param name="value">Base64字符串</param>
/// <returns></returns>
public static bool IsBase64String(string value)
{
    return value.IsMatch(@"[A-Za-z0-9\+\/\=]");
}
#endregion

2.5 时间验证

#region IsDate(是否日期)
/// <summary>
/// 是否日期
/// </summary>
/// <param name="value">日期字符串</param>
/// <param name="isRegex">是否正则验证</param>
/// <returns></returns>
public static bool IsDate(string value,bool isRegex=false)
{
    if (value.IsEmpty())
    {
        return false;
    }
    if (isRegex)
    {
        //考虑到4年一度的366天，还有特殊的2月的日期
        return
            value.IsMatch(
                @"^((((1[6-9]|[2-9]\d)\d{2})-(0?[13578]|1[02])-(0?[1-9]|[12]\d|3[01]))|(((1[6-9]|[2-9]\d)\d{2})-(0?[13456789]|1[012])-(0?[1-9]|[12]\d|30))|(((1[6-9]|[2-9]\d)\d{2})-0?2-(0?[1-9]|1\d|2[0-8]))|(((1[6-9]|[2-9]\d)(0[48]|[2468][048]|[13579][26])|((16|[2468][048]|[3579][26])00))-0?2-29-)) (20|21|22|23|[0-1]?\d):[0-5]?\d:[0-5]?\d$");
    }
    DateTime minValue;
    return DateTime.TryParse(value, out minValue);
}

/// <summary>
/// 是否日期
/// </summary>
/// <param name="value">日期字符串</param>
/// <param name="format">格式化字符串</param>
/// <returns></returns>
public static bool IsDate(string value, string format)
{
    return IsDate(value, format, null, DateTimeStyles.None);
}

/// <summary>
/// 是否日期
/// </summary>
/// <param name="value">日期字符串</param>
/// <param name="format">格式化字符串</param>
/// <param name="provider">格式化提供者</param>
/// <param name="styles">日期格式</param>
/// <returns></returns>
public static bool IsDate(string value, string format, IFormatProvider provider, DateTimeStyles styles)
{
    if (value.IsEmpty())
    {
        return false;
    }
    DateTime minValue;
    return DateTime.TryParseExact(value, format, provider, styles, out minValue);
}
#endregion

#region IsDateTime(是否有效时间)
/// <summary>
/// 是否大于最小时间
/// </summary>
/// <param name="value">时间</param>
/// <param name="min">最小时间</param>
/// <returns></returns>
public static bool IsDateTimeMin(string value, DateTime min)
{
    if (value.IsEmpty())
    {
        return false;
    }
    DateTime dateTime;
    if (DateTime.TryParse(value, out dateTime))
    {
        if (DateTime.Compare(dateTime, min) >= 0)
        {
            return true;
        }
    }
    return false;
}
/// <summary>
/// 是否小于最大时间
/// </summary>
/// <param name="value">时间</param>
/// <param name="max">最大时间</param>
/// <returns></returns>
public static bool IsDateTimeMax(string value, DateTime max)
{
    if (value.IsEmpty())
    {
        return false;
    }
    DateTime dateTime;
    if (DateTime.TryParse(value, out dateTime))
    {
        if (DateTime.Compare(max, dateTime) >= 0)
        {
            return true;
        }
    }
    return false;
}
#endregion

2.6 Url验证

#region IsUrl(是否Url地址)
/// <summary>
/// 是否Url地址（统一资源定位）
/// </summary>
/// <param name="value">url地址</param>
/// <returns></returns>
public static bool IsUrl(string value)
{
    if (value.IsEmpty())
    {
        return false;
    }
    return
        value.IsMatch(
            @"^(http|https)\://([a-zA-Z0-9\.\-]+(\:[a-zA-Z0-9\.&%\$\-]+)*@)*((25[0-5]|2[0-4][0-9]|[0-1]{1}[0-9]{2}|[1-9]{1}[0-9]{1}|[1-9])\.(25[0-5]|2[0-4][0-9]|[0-1]{1}[0-9]{2}|[1-9]{1}[0-9]{1}|[1-9]|0)\.(25[0-5]|2[0-4][0-9]|[0-1]{1}[0-9]{2}|[1-9]{1}[0-9]{1}|[1-9]|0)\.(25[0-5]|2[0-4][0-9]|[0-1]{1}[0-9]{2}|[1-9]{1}[0-9]{1}|[0-9])|localhost|([a-zA-Z0-9\-]+\.)*[a-zA-Z0-9\-]+\.(com|edu|gov|int|mil|net|org|biz|arpa|info|name|pro|aero|coop|museum|[a-zA-Z]{1,10}))(\:[0-9]+)*(/($|[a-zA-Z0-9\.\,\?\'\\\+&%\$#\=~_\-]+))*$",
            RegexOptions.IgnoreCase);
}
#endregion

#region IsUri(是否Uri)
/// <summary>
/// 是否Uri（统一资源标识）
/// </summary>
/// <param name="value">uri</param>
/// <returns></returns>
public static bool IsUri(string value)
{
    if (value.IsEmpty())
    {
        return false;
    }
    if (value.IndexOf(".", StringComparison.OrdinalIgnoreCase) == -1)
    {
        return false;
    }
    var schemes = new[]
    {
        "file",
        "ftp",
        "gopher",
        "http",
        "https",
        "ldap",
        "mailto",
        "net.pipe",
        "net.tcp",
        "news",
        "nntp",
        "telnet",
        "uuid"
    };

    bool hasValidSchema = false;
    foreach (string scheme in schemes)
    {
        if (hasValidSchema)
        {
            continue;
        }
        if (value.StartsWith(scheme, StringComparison.OrdinalIgnoreCase))
        {
            hasValidSchema = true;
        }
    }
    if (!hasValidSchema)
    {
        value = "http://" + value;
    }
    return Uri.IsWellFormedUriString(value, UriKind.Absolute);
}
#endregion

#region IsMainDomain(是否主域名)
/// <summary>
/// 是否主域名或者www开头的域名
/// </summary>
/// <param name="value">url地址</param>
/// <returns></returns>
public static bool IsMainDomain(string value)
{
    if (value.IsEmpty())
    {
        return false;
    }
    return
        value.IsMatch(
            @"^http(s)?\://((www.)?[a-zA-Z0-9\-]+\.(com|edu|gov|int|mil|net|org|biz|arpa|info|name|pro|aero|coop|museum|[a-zA-Z]{1,10}))(\:[0-9]+)*(/($|[a-zA-Z0-9\.\,\?\'\\\+&%\$#\=~_\-]+))*$");
}
#endregion

2.7 基础格式验证

#region IsGuid(是否Guid)
/// <summary>
/// 是否Guid
/// </summary>
/// <param name="guid">Guid字符串</param>
/// <returns></returns>
public static bool IsGuid(string guid)
{
    if (guid.IsEmpty())
    {
        return false;
    }
    return guid.IsMatch(@"[A-F0-9]{8}(-[A-F0-9]{4}){3}-[A-F0-9]{12}|[A-F0-9]{32}", RegexOptions.IgnoreCase);
}
#endregion

#region IsPositiveInteger(是否大于0的正整数)
/// <summary>
/// 是否大于0的正整数
/// </summary>
/// <param name="value">正整数</param>
/// <returns></returns>
public static bool IsPositiveInteger(string value)
{
    if (value.IsEmpty())
    {
        return false;
    }
    return value.IsMatch(@"^[1-9]+\d*$");
}
#endregion

#region IsInt32(是否Int32类型)
/// <summary>
/// 是否Int32类型
/// </summary>
/// <param name="value">整数</param>
/// <returns></returns>
public static bool IsInt32(string value)
{
    if (value.IsEmpty())
    {
        return false;
    }
    return value.IsMatch(@"^[0-9]*$");
}
#endregion

#region IsDouble(是否Double类型，如果带有.默认为1位0)
/// <summary>
/// 是否Double类型
/// </summary>
/// <param name="value">小数</param>
/// <returns></returns>
public static bool IsDouble(string value)
{
    if (value.IsEmpty())
    {
        return false;
    }
    return value.IsMatch(@"^\d[.]?\d?$");
}
/// <summary>
/// 是否Double类型
/// </summary>
/// <param name="value">小数</param>
/// <param name="minValue">最小值</param>
/// <param name="maxValue">最大值</param>
/// <param name="digit">小数位数，如果是0则不检测</param>
/// <returns></returns>
public static bool IsDouble(string value, double minValue, double maxValue, int digit)
{
    if (value.IsEmpty())
    {
        return false;
    }
    string patten = string.Format(@"^\d[.]?\d{0}$", "{0,10}");
    if (digit > 0)
    {
        patten = string.Format(@"^\d[.]?\d{0}$", "{" + digit + "}");
    }
    if (value.IsMatch(patten))
    {
        double val = Convert.ToDouble(value);
        if (val >= minValue && val <= maxValue)
        {
            return true;
        }
    }
    return false;
}
#endregion

#region IsInteger(是否整数)
/// <summary>
/// 是否整数
/// </summary>
/// <param name="value">值</param>
/// <returns>结果</returns>
public static bool IsInteger(string value)
{
    if (value.IsEmpty())
    {
        return false;
    }
    return value.IsMatch(@"^\-?[0-9]+$");
}
#endregion

#region IsUnicode(是否Unicode字符串)
/// <summary>
/// 是否Unicode字符串
/// </summary>
/// <param name="value">unicode字符串</param>
/// <returns>结果</returns>
public static bool IsUnicode(string value)
{
    if (value.IsEmpty())
    {
        return false;
    }
    return
        value.IsMatch(
            @"^(http|https|ftp|rtsp|mms):(\/\/|\\\\)[A-Za-z0-9%\-_@]+\.[A-Za-z0-9%\-_@]+[A-Za-z0-9\.\/=\?%\-&_~`@:\+!;]*$");
}
#endregion

#region IsDecimal(是否数字型)
/// <summary>
/// 是否数字型
/// </summary>
/// <param name="value">数字</param>
/// <returns></returns>
public static bool IsDecimal(string value)
{
    if (value.IsEmpty())
    {
        return false;
    }
    return value.IsMatch(@"^([0-9])[0-9]*(\.\w*)?$");
}
#endregion

2.8 IP、Mac地址验证

#region IsMac(是否Mac地址)
/// <summary>
/// 是否Mac地址
/// </summary>
/// <param name="value">Mac地址</param>
/// <returns></returns>
public static bool IsMac(string value)
{
    if (value.IsEmpty())
    {
        return false;
    }
    return value.IsMatch(@"^([0-9A-F]{2}-){5}[0-9A-F]{2}$") || value.IsMatch(@"^[0-9A-F]{12}$");
}
#endregion

#region IsIpAddress(是否IP地址)
/// <summary>
/// 是否IP地址
/// </summary>
/// <param name="value">ip地址</param>
/// <returns>结果</returns>
public static bool IsIpAddress(string value)
{
    if (value.IsEmpty())
    {
        return false;
    }
    return value.IsMatch(@"^(\d(25[0-5]|2[0-4][0-9]|1?[0-9]?[0-9])\d\.){3}\d(25[0-5]|2[0-4][0-9]|1?[0-9]?[0-9])\d$");
}
#endregion

2.9 字符串验证

#region IsVersion(是否有效的版本号)
/// <summary>
/// 是否有效版本号，范例：1.3,1.1.5,1.25.256
/// </summary>
/// <param name="value">版本号</param>
/// <param name="length">长度</param>
/// <returns></returns>
public static bool IsVersion(string value, int length = 5)
{
    if (value.IsEmpty())
    {
        return false;
    }
    value = value.Replace("^", "").Replace("$", "");
    return value.IsMatch(string.Format(@"^{0}{1}{2}$", @"\d{0,4}\.(\d{1,4}\.){0,", length, @"}\d{1,4}"));
}
#endregion

#region IsContainsChinese(是否包含中文)
/// <summary>
/// 是否中文
/// </summary>
/// <param name="value">中文</param>
/// <returns></returns>
public static bool IsChinese(string value)
{
    if (value.IsEmpty())
    {
        return false;
    }
    return value.IsMatch(@"^[\u4e00-\u9fa5]+$", RegexOptions.IgnoreCase);
}
/// <summary>
/// 是否包含中文
/// </summary>
/// <param name="value">中文</param>
/// <returns></returns>
public static bool IsContainsChinese(string value)
{
    if (value.IsEmpty())
    {
        return false;
    }
    return value.IsMatch(@"[\u4e00-\u9fa5]+",RegexOptions.IgnoreCase);
}
#endregion

#region IsContainsNumber(是否包含数字)
/// <summary>
/// 是否包含数字
/// </summary>
/// <param name="value">数字</param>
/// <returns></returns>
public static bool IsContainsNumber(string value)
{
    if (value.IsEmpty())
    {
        return false;
    }
    return value.IsMatch(@"[0-9]+");
}
#endregion
#region IsLengthStr(字符串长度是否在指定范围内)
/// <summary>
/// 字符串长度是否在指定范围内，一个中文为2个字符
/// </summary>
/// <param name="value">字符串</param>
/// <param name="begin">开始</param>
/// <param name="end">结束</param>
/// <returns></returns>
public static bool IsLengthStr(string value, int begin, int end)
{
    int length = Regex.Replace(value, @"[^\x00-\xff]", "OK").Length;
    if ((length <= begin) && (length >= end))
    {
        return false;
    }
    return true;
}
#endregion

#region IsNormalChar(是否正常字符，字母、数字、下划线的组合)
/// <summary>
/// 是否正常字符，字母、数字、下划线的组合
/// </summary>
/// <param name="value">字符串</param>
/// <returns></returns>
public static bool IsNormalChar(string value)
{
    if (value.IsEmpty())
    {
        return false;
    }
    return value.IsMatch(@"[\w\d_]+", RegexOptions.IgnoreCase);
}
#endregion

#region IsPostfix(是否指定后缀)
/// <summary>
/// 是否指定后缀
/// </summary>
/// <param name="value">字符串</param>
/// <param name="postfixs">后缀名数组</param>
/// <returns></returns>
public static bool IsPostfix(string value, string[] postfixs)
{
    if (value.IsEmpty())
    {
        return false;
    }
    string postfix = string.Join("|", postfixs);
    return value.IsMatch(string.Format(@".(?i:{0})$", postfix));
}
#endregion

#region IsRepeat(是否重复)
/// <summary>
/// 是否重复，范例：112,返回true
/// </summary>
/// <param name="value">值</param>
/// <returns></returns>
public static bool IsRepeat(string value)
{
    if (value.IsEmpty())
    {
        return false;
    }
    var array = value.ToCharArray();
    return array.Any(c => array.Count(t => t == c) > 1);
}
#endregion

2.10 邮政编码验证

#region IsPostalCode(是否邮政编码)
/// <summary>
/// 是否邮政编码，6位数字
/// </summary>
/// <param name="value">邮政编码</param>
/// <returns></returns>
public static bool IsPostalCode(string value)
{
    if (value.IsEmpty())
    {
        return false;
    }
    return value.IsMatch(@"^[1-9]\d{5}$", RegexOptions.IgnoreCase);
}
#endregion

2.11 中国固话验证

#region IsTel(是否中国电话)
/// <summary>
/// 是否中国电话，格式：010-85849685
/// </summary>
/// <param name="value">电话</param>
/// <returns></returns>
public static bool IsTel(string value)
{
    if (value.IsEmpty())
    {
        return false;
    }
    return value.IsMatch(@"^\d{3,4}-?\d{6,8}$", RegexOptions.IgnoreCase);
}
#endregion

2.12 QQ号码验证

#region IsQQ(是否合法QQ号码)
/// <summary>
/// 是否合法QQ号码
/// </summary>
/// <param name="value">QQ号码</param>
/// <returns></returns>
// ReSharper disable once InconsistentNaming
public static bool IsQQ(string value)
{
    if (value.IsEmpty())
    {
        return false;
    }
    return value.IsMatch(@"^[1-9][0-9]{4,9}$");
}
#endregion

三、源代码

最后 附上源码地址 https://github.com/jianxuanbing/JCE/blob/master/JCE.Utils/Valid.cs
四、正则表达式
4.1 校验数字的表达式

数字：^[0-9]*$
n位的数字：^\d{n}$
至少n位的数字：^\d{n,}$
m-n位的数字：^\d{m,n}$
零和非零开头的数字：^(0|[1-9][0-9]*)$
非零开头的最多带两位小数的数字：^([1-9][0-9]*)+(.[0-9]{1,2})?$
带1-2位小数的正数或负数：^(\-)?\d+(\.\d{1,2})?$
正数、负数、和小数：^(\-|\+)?\d+(\.\d+)?$
有两位小数的正实数：^[0-9]+(.[0-9]{2})?$
有1~3位小数的正实数：^[0-9]+(.[0-9]{1,3})?$
非零的正整数：^[1-9]\d*$ 或 ^([1-9][0-9]*){1,3}$ 或 ^\+?[1-9][0-9]*$
非零的负整数：^\-[1-9][]0-9"*$ 或 ^-[1-9]\d*$
非负整数：^\d+$ 或 ^[1-9]\d*|0$
非正整数：^-[1-9]\d*|0$ 或 ^((-\d+)|(0+))$
非负浮点数：^\d+(\.\d+)?$ 或 ^[1-9]\d*\.\d*|0\.\d*[1-9]\d*|0?\.0+|0$
非正浮点数：^((-\d+(\.\d+)?)|(0+(\.0+)?))$ 或 ^(-([1-9]\d*\.\d*|0\.\d*[1-9]\d*))|0?\.0+|0$
正浮点数：^[1-9]\d*\.\d*|0\.\d*[1-9]\d*$ 或 ^(([0-9]+\.[0-9]*[1-9][0-9]*)|([0-9]*[1-9][0-9]*\.[0-9]+)|([0-9]*[1-9][0-9]*))$
负浮点数：^-([1-9]\d*\.\d*|0\.\d*[1-9]\d*)$ 或 ^(-(([0-9]+\.[0-9]*[1-9][0-9]*)|([0-9]*[1-9][0-9]*\.[0-9]+)|([0-9]*[1-9][0-9]*)))$
浮点数：^(-?\d+)(\.\d+)?$ 或 ^-?([1-9]\d*\.\d*|0\.\d*[1-9]\d*|0?\.0+|0)$

4.2 校验字符的表达式

汉字：^[\u4e00-\u9fa5]{0,}$
英文和数字：^[A-Za-z0-9]+$ 或 ^[A-Za-z0-9]{4,40}$
长度为3-20的所有字符：^.{3,20}$
由26个英文字母组成的字符串：^[A-Za-z]+$
由26个大写英文字母组成的字符串：^[A-Z]+$
由26个小写英文字母组成的字符串：^[a-z]+$
由数字和26个英文字母组成的字符串：^[A-Za-z0-9]+$
由数字、26个英文字母或者下划线组成的字符串：^\w+$ 或 ^\w{3,20}$
中文、英文、数字包括下划线：^[\u4E00-\u9FA5A-Za-z0-9_]+$
中文、英文、数字但不包括下划线等符号：^[\u4E00-\u9FA5A-Za-z0-9]+$ 或 ^[\u4E00-\u9FA5A-Za-z0-9]{2,20}$
可以输入含有^%&',;=?$\"等字符：[^%&',;=?$\x22]+
禁止输入含有~的字符：[^~\x22]+

4.3 特殊需求表达式

Email地址：^\w+([-+.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*$
域名：[a-zA-Z0-9][-a-zA-Z0-9]{0,62}(/.[a-zA-Z0-9][-a-zA-Z0-9]{0,62})+/.?
InternetURL：[a-zA-z]+://[^\s]* 或 ^http://([\w-]+\.)+[\w-]+(/[\w-./?%&=]*)?$
手机号码：^(13[0-9]|14[5|7]|15[0|1|2|3|5|6|7|8|9]|18[0|1|2|3|5|6|7|8|9])\d{8}$
电话号码("XXX-XXXXXXX"、"XXXX-XXXXXXXX"、"XXX-XXXXXXX"、"XXX-XXXXXXXX"、"XXXXXXX"和"XXXXXXXX)：^(\(\d{3,4}-)|\d{3.4}-)?\d{7,8}$ 
国内电话号码(0511-4405222、021-87888822)：\d{3}-\d{8}|\d{4}-\d{7}
身份证号(15位、18位数字)：^\d{15}|\d{18}$
短身份证号码(数字、字母x结尾)：^([0-9]){7,18}(x|X)?$ 或 ^\d{8,18}|[0-9x]{8,18}|[0-9X]{8,18}?$
帐号是否合法(字母开头，允许5-16字节，允许字母数字下划线)：^[a-zA-Z][a-zA-Z0-9_]{4,15}$
密码(以字母开头，长度在6~18之间，只能包含字母、数字和下划线)：^[a-zA-Z]\w{5,17}$
强密码(必须包含大小写字母和数字的组合，不能使用特殊字符，长度在8-10之间)：^(?=.*\d)(?=.*[a-z])(?=.*[A-Z]).{8,10}$  
日期格式：^\d{4}-\d{1,2}-\d{1,2}
一年的12个月(01～09和1～12)：^(0?[1-9]|1[0-2])$
一个月的31天(01～09和1～31)：^((0?[1-9])|((1|2)[0-9])|30|31)$ 
钱的输入格式：
xml文件：^([a-zA-Z]+-?)+[a-zA-Z0-9]+\\.[x|X][m|M][l|L]$
中文字符的正则表达式：[\u4e00-\u9fa5]
双字节字符：[^\x00-\xff]    (包括汉字在内，可以用来计算字符串的长度(一个双字节字符长度计2，ASCII字符计1))
空白行的正则表达式：\n\s*\r    (可以用来删除空白行)
HTML标记的正则表达式：<(\S*?)[^>]*>.*?</\1>|<.*? />    (网上流传的版本太糟糕，上面这个也仅仅能部分，对于复杂的嵌套标记依旧无能为力)
首尾空白字符的正则表达式：^\s*|\s*$或(^\s*)|(\s*$)    (可以用来删除行首行尾的空白字符(包括空格、制表符、换页符等等)，非常有用的表达式)
腾讯QQ号：[1-9][0-9]{4,}    (腾讯QQ号从10000开始)
中国邮政编码：[1-9]\d{5}(?!\d)    (中国邮政编码为6位数字)
IP地址：\d+\.\d+\.\d+\.\d+    (提取IP地址时有用)
IP地址：((?:(?:25[0-5]|2[0-4]\\d|[01]?\\d?\\d)\\.){3}(?:25[0-5]|2[0-4]\\d|[01]?\\d?\\d))

4.4 钱的输入格式

有四种钱的表示形式我们可以接受:"10000.00" 和 "10,000.00", 和没有 "分" 的 "10000" 和 "10,000"：^[1-9][0-9]*$
这表示任意一个不以0开头的数字,但是,这也意味着一个字符"0"不通过,所以我们采用下面的形式：^(0|[1-9][0-9]*)$
一个0或者一个不以0开头的数字.我们还可以允许开头有一个负号：^(0|-?[1-9][0-9]*)$
这表示一个0或者一个可能为负的开头不为0的数字.让用户以0开头好了.把负号的也去掉,因为钱总不能是负的吧.下面我们要加的是说明可能的小数部分：^[0-9]+(.[0-9]+)?$
必须说明的是,小数点后面至少应该有1位数,所以"10."是不通过的,但是 "10" 和 "10.2" 是通过的：^[0-9]+(.[0-9]{2})?$
这样我们规定小数点后面必须有两位,如果你认为太苛刻了,可以这样：^[0-9]+(.[0-9]{1,2})?$
这样就允许用户只写一位小数.下面我们该考虑数字中的逗号了,我们可以这样：^[0-9]{1,3}(,[0-9]{3})*(.[0-9]{1,2})?$
1到3个数字,后面跟着任意个 逗号+3个数字,逗号成为可选,而不是必须：^([0-9]+|[0-9]{1,3}(,[0-9]{3})*)(.[0-9]{1,2})?$
备注：这就是最终结果了,别忘了"+"可以用"*"替代如果你觉得空字符串也可以接受的话(奇怪,为什么?)最后,别忘了在用函数时去掉去掉那个反斜杠,一般的错误都在这里

## 五、工具站

[在线正则表达式验证](http://regex.zjmainstay.cn/)


1 . 校验密码强度

密码的强度必须是包含大小写字母和数字的组合，不能使用特殊字符，长度在8-10之间。

^(?=.*\\d)(?=.*[a-z])(?=.*[A-Z]).{8,10}$

2. 校验中文

字符串仅能是中文。

^[\\u4e00-\\u9fa5]{0,}$

3. 由数字、26个英文字母或下划线组成的字符串

^\\w+$

4. 校验E-Mail 地址

同密码一样，下面是E-mail地址合规性的正则检查语句。

[\\w!#$%&'*+/=?^_`{|}~-]+(?:\\.[\\w!#$%&'*+/=?^_`{|}~-]+)*@(?:[\\w](?:[\\w-]*[\\w])?\\.)+[\\w](?:[\\w-]*[\\w])?

5. 校验身份证号码

下面是身份证号码的正则校验。15 或 18位。

15位：

^[1-9]\\d{7}((0\\d)|(1[0-2]))(([0|1|2]\\d)|3[0-1])\\d{3}$

18位：

^[1-9]\\d{5}[1-9]\\d{3}((0\\d)|(1[0-2]))(([0|1|2]\\d)|3[0-1])\\d{3}([0-9]|X)$

6. 校验日期

“yyyy-mm-dd“ 格式的日期校验，已考虑平闰年。

^(?:(?!0000)[0-9]{4}-(?:(?:0[1-9]|1[0-2])-(?:0[1-9]|1[0-9]|2[0-8])|(?:0[13-9]|1[0-2])-(?:29|30)|(?:0[13578]|1[02])-31)|(?:[0-9]{2}(?:0[48]|[2468][048]|[13579][26])|(?:0[48]|[2468][048]|[13579][26])00)-02-29)$

7. 校验金额

金额校验，精确到2位小数。

^[0-9]+(.[0-9]{2})?$

8. 校验手机号

下面是国内 13、15、18开头的手机号正则表达式。（可根据目前国内收集号扩展前两位开头号码）

^(13[0-9]|14[5|7]|15[0|1|2|3|5|6|7|8|9]|18[0|1|2|3|5|6|7|8|9])\\d{8}$

9. 判断IE的版本

IE目前还没被完全取代，很多页面还是需要做版本兼容，下面是IE版本检查的表达式。

^.*MSIE [5-8](?:\\.[0-9]+)?(?!.*Trident\\/[5-9]\\.0).*$

10. 校验IP-v4地址

IP4 正则语句。

\\b(?:(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\\.){3}(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\\b

11. 校验IP-v6地址

IP6 正则语句。

(([0-9a-fA-F]{1,4}:){7,7}[0-9a-fA-F]{1,4}|([0-9a-fA-F]{1,4}:){1,7}:|([0-9a-fA-F]{1,4}:){1,6}:[0-9a-fA-F]{1,4}|([0-9a-fA-F]{1,4}:){1,5}(:[0-9a-fA-F]{1,4}){1,2}|([0-9a-fA-F]{1,4}:){1,4}(:[0-9a-fA-F]{1,4}){1,3}|([0-9a-fA-F]{1,4}:){1,3}(:[0-9a-fA-F]{1,4}){1,4}|([0-9a-fA-F]{1,4}:){1,2}(:[0-9a-fA-F]{1,4}){1,5}|[0-9a-fA-F]{1,4}:((:[0-9a-fA-F]{1,4}){1,6})|:((:[0-9a-fA-F]{1,4}){1,7}|:)|fe80:(:[0-9a-fA-F]{0,4}){0,4}%[0-9a-zA-Z]{1,}|::(ffff(:0{1,4}){0,1}:){0,1}((25[0-5]|(2[0-4]|1{0,1}[0-9]){0,1}[0-9])\\.){3,3}(25[0-5]|(2[0-4]|1{0,1}[0-9]){0,1}[0-9])|([0-9a-fA-F]{1,4}:){1,4}:((25[0-5]|(2[0-4]|1{0,1}[0-9]){0,1}[0-9])\\.){3,3}(25[0-5]|(2[0-4]|1{0,1}[0-9]){0,1}[0-9]))

12. 检查URL的前缀

应用开发中很多时候需要区分请求是HTTPS还是HTTP，通过下面的表达式可以取出一个url的前缀然后再逻辑判断。

if (!s.match(/^[a-zA-Z]+:\\/\\//))
{
    s = 'http://' + s;
}

13. 提取URL链接

下面的这个表达式可以筛选出一段文本中的URL。

^(f|ht){1}(tp|tps):\\/\\/([\\w-]+\\.)+[\\w-]+(\\/[\\w- ./?%&=]*)?

14. 文件路径及扩展名校验

验证windows下文件路径和扩展名（下面的例子中为.txt文件）

^([a-zA-Z]\\:|\\\\)\\\\([^\\\\]+\\\\)*[^\\/:*?"<>|]+\\.txt(l)?$

15. 提取Color Hex Codes

有时需要抽取网页中的颜色代码，可以使用下面的表达式。

^#([A-Fa-f0-9]{6}|[A-Fa-f0-9]{3})$

16. 提取网页图片

假若你想提取网页中所有图片信息，可以利用下面的表达式。

\\< *[img][^\\\\>]*[src] *= *[\\"\\']{0,1}([^\\"\\'\\ >]*)

17. 提取页面超链接

提取html中的超链接。

(<a\\s*(?!.*\\brel=)[^>]*)(href="https?:\\/\\/)((?!(?:(?:www\\.)?'.implode('|(?:www\\.)?', $follow_list).'))[^"]+)"((?!.*\\brel=)[^>]*)(?:[^>]*)>

18. 查找CSS属性

通过下面的表达式，可以搜索到相匹配的CSS属性。

^\\s*[a-zA-Z\\-]+\\s*[:]{1}\\s[a-zA-Z0-9\\s.#]+[;]{1}

19. 抽取注释

如果你需要移除HMTL中的注释，可以使用如下的表达式。

<!--(.*?)-->

20. 匹配HTML标签

通过下面的表达式可以匹配出HTML中的标签属性。

<\\/?\\w+((\\s+\\w+(\\s*=\\s*(?:".*?"|'.*?'|[\\^'">\\s]+))?)+\\s*|\\s*)\\/?>