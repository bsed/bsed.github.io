---
title: "java中数据类型转换[java学习笔记]"
categories: [ "Java" ]
tags: [ "java","string","int" ]
draft: false
slug: "java-data-type-conversion-java-learning-notes"
date: "2011-06-29 13:13:00"
---


## 1.如何将字串 String 转换成整数 int?
 
A. 有两个方法:
 
1). int i = Integer.parseInt([String]); 或
i = Integer.parseInt([String],[int radix]);
 
2). int i = Integer.valueOf(my_str).intValue();
 
注: 字串转成 Double, Float, Long 的方法大同小异.
 

2 如何将整数 int 转换成字串 String ?
 


<!--more-->


A. 有叁种方法:
 
1.) String s = String.valueOf(i);
 
2.) String s = Integer.toString(i);
 
3.) String s = "" + i;
 
注: Double, Float, Long 转成字串的方法大同小异.
 
 
 
JAVA数据类型转换     ynniebo [收藏] 
关键字   类型转换
出处   
 
 这是一个例子,说的是JAVA中数据数型的转换.供大家学习引
 

    package cn.com.lwkj.erts.register;
    import java.sql.Date;
    public class TypeChange {
      public TypeChange() {
      }
      //change the string type to the int type
      public static  int stringToInt(String intstr)
      {
        Integer integer;
        integer = Integer.valueOf(intstr);
        return integer.intValue();
      }
      //change int type to the string type
      public static String intToString(int value)
      {
        Integer integer = new Integer(value);
        return integer.toString();
      }
      //change the string type to the float type
      public static  float stringToFloat(String floatstr)
      {
        Float floatee;
        floatee = Float.valueOf(floatstr);
        return floatee.floatValue();
      }
      //change the float type to the string type
      public static String floatToString(float value)
      {
        Float floatee = new Float(value);
        return floatee.toString();
      }
      //change the string type to the sqlDate type
      public static java.sql.Date stringToDate(String dateStr)
      {
        return  java.sql.Date.valueOf(dateStr);
      }
      //change the sqlDate type to the string type
      public static String dateToString(java.sql.Date datee)
      {
        return datee.toString();
      }
     
      public static void main(String[] args)
      {
        java.sql.Date day ;
        day = TypeChange.stringToDate("2003-11-3");
        String strday = TypeChange.dateToString(day);
        System.out.println(strday);
      }
     
    
    }

 

JAVA中常用数据类型转换函数
虽然都能在JAVA API中找到，整理一下做个备份。
 

    string->byte
    Byte static byte parseByte(String s) 
     
    byte->string
    Byte static String toString(byte b)
     
    char->string
    Character static String to String (char c)
     
    string->Short
    Short static Short parseShort(String s)
     
    Short->String
    Short static String toString(Short s)
     
    String->Integer
    Integer static int parseInt(String s)
     
    Integer->String
    Integer static String tostring(int i)
     
    String->Long
    Long static long parseLong(String s)
     
    Long->String
    Long static String toString(Long i)
     
    String->Float
    Float static float parseFloat(String s)
     
    Float->String
    Float static String toString(float f)
     
    String->Double
    Double static double parseDouble(String s)
     
    Double->String
    Double static String toString(Double)

++++++++++++++++++++++++++++++++++++++++++++++++++++++

**数据类型**

基本类型有以下四种：
int长度数据类型有：byte(8bits)、short(16bits)、int(32bits)、long(64bits)、
float长度数据类型有：单精度（32bits float）、双精度（64bits double）
boolean类型变量的取值有：ture、false
char数据类型有：unicode字符,16位
对应的类类型：Integer、Float、Boolean、Character、Double、Short、Byte、Long

**转换原则**

### 从低精度向高精度转换
byte 、short、int、long、float、double、char
注：两个char型运算时，自动转换为int型；当char与别的类型运算时，也会先自动转换为int型的，再做其它类型的自动转换

### 基本类型向类类型转换

正向转换：通过类包装器来new出一个新的类类型的变量
Integer a= new Integer(2);
反向转换：通过类包装器来转换
int b=a.intValue();

### 类类型向字符串转换

正向转换：因为每个类都是object类的子类，而所有的object类都有一个toString()函数，所以通过toString()函数来转换即可
反向转换：通过类包装器new出一个新的类类型的变量
eg1: int i=Integer.valueOf(“123”).intValue()
说明：上例是将一个字符串转化成一个Integer对象，然后再调用这个对象的intValue()方法返回其对应的int数值。
eg2: float f=Float.valueOf(“123”).floatValue()
说明：上例是将一个字符串转化成一个Float对象，然后再调用这个对象的floatValue()方法返回其对应的float数值。
eg3: boolean b=Boolean.valueOf(“123”).booleanValue()
说明：上例是将一个字符串转化成一个Boolean对象，然后再调用这个对象的booleanValue()方法返回其对应的boolean数值。
eg4:double d=Double.valueOf(“123”).doubleValue()
说明：上例是将一个字符串转化成一个Double对象，然后再调用这个对象的doubleValue()方法返回其对应的double数值。
eg5: long l=Long.valueOf(“123”).longValue()
说明：上例是将一个字符串转化成一个Long对象，然后再调用这个对象的longValue()方法返回其对应的long数值。
eg6: char=Character.valueOf(“123”).charValue()
说明：上例是将一个字符串转化成一个Character对象，然后再调用这个对象的charValue()方法返回其对应的char数值。

基本类型向字符串的转换
正向转换：
如：int a=12;
String b;b=a+””;

**反向转换：**
通过类包装器
eg1:int i=Integer.parseInt(“123”)
说明：此方法只能适用于字符串转化成整型变量
eg2: float f=Float.valueOf(“123”).floatValue()
说明：上例是将一个字符串转化成一个Float对象，然后再调用这个对象的floatValue()方法返回其对应的float数值。
eg3: boolean b=Boolean.valueOf(“123”).booleanValue()
说明：上例是将一个字符串转化成一个Boolean对象，然后再调用这个对象的booleanValue()方法返回其对应的boolean数值。
eg4:double d=Double.valueOf(“123”).doubleValue()
说明：上例是将一个字符串转化成一个Double对象，然后再调用这个对象的doubleValue()方法返回其对应的double数值。
eg5: long l=Long.valueOf(“123”).longValue()
说明：上例是将一个字符串转化成一个Long对象，然后再调用这个对象的longValue()方法返回其对应的long数值。
eg6: char=Character.valueOf(“123”).charValue()
说明：上例是将一个字符串转化成一个Character对象，然后再调用这个对象的charValue()方法返回其对应的char数值。

