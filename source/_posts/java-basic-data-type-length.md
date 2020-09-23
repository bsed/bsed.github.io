---
title: JAVA 基本数据类型长度
date: 2010-09-26 11:20:00
updated: 2015-09-26 11:23:10
tags: 
- java
categories: 
- java

---
Java语言提供了八种基本类型。六种数字类型（四个整数型，两个浮点型），一种字符类型，还有一种布尔型。 
1、整数：包括int,short,byte,long 
2、浮点型：float,double 
3、字符：char 
4、布尔：boolean

| 基本型别 | 大小 | 最小值 | 最大值 |
| -- | -- | -- | -- | 
| boolean | ----- | ----- | ------ |  
| char | 16-bit | Unicode 0 | Unicode 2^16-1 |  
| byte | 8-bit | -128 | +127 |  
| short | 16-bit | -2^15 | +2^15-1 | 
| int | 32-bit | -2^31 | +2^31-1 |  
| long | 64-bit | -2^63 | +2^63-1 |  
| float | 32-bit | IEEE754 | IEEE754 | 
| double | 64-bit | IEEE754 | IEEE754 |  
| void   |   |    |   | 

**注意**！：表格里的^代表的是次方哈～


<!--more-->


使用 Float 类 节省开发时间 
作者： Builder.com 
2004-11-16 11:50 AM

即使你可能知道 IEEE 浮点数，你可能也从来没有逐个比特地处理过这类数据。下面我将向你展示如何逐个比特地处理 IEEE 浮点数，下次在你的 Java 项目需要从比特序列创建浮点数时，或者从浮点数创建比特序列时，你就知道Float 类可以为完成这一工作。

### IEEE 浮点数介绍

IEEE 754 浮点单精度数字格式定义了一个用于存储浮点数的比特布局。在空比特布局中，一个比特留作符号位，八个比特留作指数，23个比特留作尾数。这些比特是按最重要比特到最不重要比特进行排列的，如下例所示：



31                             0
|                              |                      
SEEEEEEEEMMMMMMMMMMMMMMMMMMMMMMM

其中：

S = 符号位
E = 指数
M = 尾数

你可以从 IEEE 的站点购买一份 IEEE 浮点数的完整解释。（如果在线搜索 IEEE 754，你应该可以找到免费的版本。）

## Float 对 Java 社区的贡献

下面的示例程序使用 Float 的两个方法将一个浮点数转成比特，然后又将这些比特转成一个浮点数。

使用Float.floatToIntBits(float f) 方法可以将一个浮点数转成一个比特序列。这个方法返回一个32位整数，表示你作为参数提供的浮点数的 IEEE 754 比特排列。

使用Float.intBitsToFloat(int bits) 方法可以进行反方向转换。这个方法接受传入的整数作为参数，将这些比特转成一个 IEEE 浮点数。

下面是示例程序：
```
public class BitsTip {
    public static void main(String args[]) {
        float f = Float.parseFloat(args[0]);
        int bits = Float.floatToIntBits(f);

        System.out.println("bits: " + bits);
        System.out.println("back to float: " + Float.intBitsToFloat(bits));
    }
}
```
如果你曾经必须要手工进行这种转换，那么你就会很欣赏这两个简单的方法为你节省的工作。如果你正在处理64位数，那么可以选择使用Double 包装器（wrapper）类。这个类提供同样的方法处理IEEE 754 双精度浮点数。

最好选择阅读一下javadoc，查阅里面有关浮点数与比特序列之间的转换的部分，以便完整地了解这些方法都能为你些做什么。
