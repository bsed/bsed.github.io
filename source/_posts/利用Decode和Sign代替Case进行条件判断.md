---
title: "利用Decode和Sign代替Case进行条件判断"
categories: [ "Java","SQL" ]
tags: [ "decode" ]
draft: false
slug: "decode-sign-case"
date: "2014-08-28 09:21:00"
---

在Oracle的SQL条件判断中，多数情况下，因其灵活方便且可读性强，会使用Case When进行。但Decode函数使用起来简洁直观，且可在同一行进行判断，故在多数SQL条件判断中，我一直力求使用Decode。本文基于项目实战，简单谈谈利用Decode和Sign代替Case进行条件判断。

先谈谈项目：比较产品的返修时间(RepairTime)与保修截止时间(WarrantyTime)，如返修时间在保修截止时间之前(或相同)，即RepairTime=< WarrantyTime，则该产品在保修期内(IsWarranty)，


<!--more-->


设栏位IsWarranty值为Y；返修时间在保修截止时间之后，即RepairTime>WarrantyTime，则设为N。即达到以下效果图：

利用Decode和Sign代替Case进行条件判断

以上需求涉及到SQL中的条件判断，若不使用CASE判断，如何进行？答案如下：
	
    SELECT WarrantyTime,RepairTime,
    DECODE(SIGN(TO_DATE(WarrantyTime,'YYYY-MM-DD') - TO_DATE(RepairTime,'YYYY-MM-DD')),-1,'N','Y') AS IsWAR
    FROM tProductInfo

对于以上SQL，简单补习一下Decode和Sign两函数的用法，若读者已经对Decode和Sign函数很熟悉，下文可忽略不看。
Sign函数

    函数语法：Sign(n)；
    函数说明：取数字n的符号,大于0返回1,小于0返回-1,等于0返回0；
    函数示例：select sign(100),sign(-100),sign(0) from dual; 返回结果分别为： 1,-1,0

Decode函数

    函数语法：DECODE(input_value,value,result[,value,result…][,default_result])；
    函数说明：input_value 试图处理的数值；DECODE函数将该数值与一系列的序偶相比较，以决定最后的返回结果value是一组成序偶的数值；如果输入数值与之匹配成功，则相应的结果将被返回；result 是一组成序偶的结果值；default_result 未能与任何一序偶匹配成功时，函数返回的默认值；
    函数示例：对于本文实战项目有 SELECT DECODE(IsWar,-1,'N','Y') from dual;


