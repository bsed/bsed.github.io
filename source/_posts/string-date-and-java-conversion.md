---
title: java String和Date转换
date: 2010-09-03 12:54:00
updated: 2015-09-18 12:55:06
tags: 
- hibernate
- spring
categories: 
- java

---
## java String和Date转换
 

    /**
        SimpleDateFormat函数语法：
      
             G 年代标志符
             y 年
             M 月
             d 日
             h 时 在上午或下午 (1~12)
             H 时 在一天中 (0~23)
             m 分
             s 秒
             S 毫秒
             E 星期
             D 一年中的第几天
             F 一月中第几个星期几
             w 一年中第几个星期
             W 一月中第几个星期
             a 上午 / 下午 标记符
             k 时 在一天中 (1~24)
             K 时 在上午或下午 (0~11)
             z 时区
    常见标准的写法"yyyy-MM-dd HH:mm:ss",注意大小写，时间是24小时制，24小时制转换成12小时制只需将HH改成hh,不需要另外的函数。


<!--more-->


    */
    DateFormat format1 = new SimpleDateFormat("yyyy-MM-dd");        
    DateFormat format 2= new SimpleDateFormat("yyyy年MM月dd日 HH时mm分ss秒");        
    Date date = null;   
    String str = null;                 
               
    // String转Date   
    str = "2007-1-18";         
    try {   
               date = format1.parse(str);  
               data = format2.parse(str);
    } catch (ParseException e) {   
               e.printStackTrace();   
    }   
            
    //Date转String
    date=new Date();
    str=format1.format(date);
    str=format2.format(date);

1、Date——>String
String sdate;
Date ddate;
……
sdate=(new SimpleDateFormat("yyyy-MM-dd")).format(ddate);
2、String——>Date
SimpleDateFormat sdf=new SimpleDateFormat("yyyy-MM-dd");
sdf.setLenient(false);
String sdate="2006-06-06";
Date ddate;
ddate=sdf.parse(sdate);
另外Integer、Double等数据包装类型与String类型的相互转换为：
（以Integer为例）
Integer——>String
Integer ii;
String si;
……
si=ii.toString;
String——>Intteger
Integer ii;
String si;
……
ii=Integer.valueOf(si);
附：
日期和时间模式(摘自JDK1.50 DOC)

日期和时间格式由日期和时间模式 字符串指定。在日期和时间模式字符串中，未加引号的字母 'A' 到 'Z' 和 'a' 到 'z' 被解释为模式字母，用来表示日期或时间字符串元素。文本可以使用单引号 (') 引起来，以免进行解释。"''" 表示单引号。所有其他字符均不解释；只是在格式化时将它们简单复制到输出字符串，或者在分析时与输入字符串进行匹配。

定义了以下模式字母（所有其他字符 'A' 到 'Z' 和 'a' 到 'z' 都被保留）：

    字母 	日期或时间元素 	表示 	示例
    G 	Era 标志符 	Text 	AD
    y 	年 	Year 	1996; 96
    M 	年中的月份 	Month 	July; Jul; 07
    w 	年中的周数 	Number 	27
    W 	月份中的周数 	Number 	2
    D 	年中的天数 	Number 	189
    d 	月份中的天数 	Number 	10
    F 	月份中的星期 	Number 	2
    E 	星期中的天数 	Text 	Tuesday; Tue
    a 	Am/pm 标记 	Text 	PM
    H 	一天中的小时数（0-23） 	Number 	0
    k 	一天中的小时数（1-24） 	Number 	24
    K 	am/pm 中的小时数（0-11） 	Number 	0
    h 	am/pm 中的小时数（1-12） 	Number 	12
    m 	小时中的分钟数 	Number 	30
    s 	分钟中的秒数 	Number 	55
    S 	毫秒数 	Number 	978
    z 	时区 	General time zone 	Pacific Standard Time; PST; GMT-08:00
    Z 	时区 	RFC 822 time zone 	-0800