---
title: "String和StringBuffer解析 "
categories: [ "Java" ]
tags: [ "java","string","StringBuffer" ]
draft: false
slug: "string-and-stringbuffer-analysis"
date: "2011-09-18 13:32:00"
---

今天有个朋友问到这样一个问题:String和StringBuffer的区别是什么?
 我当时也一时说不完.你就看看这编文章应该是可以全然明白的.
网络资料共享:
 

### String的创建

  String s = "hello";
  jvm先根据内容"hello"查找对象，如果没有找到，则在heap上创建新对象，并将其赋予s，否则使用已经存在的对象

  String s = new String("hello");
  jvm直接在heap上创建新的对象，所以在heap中会出现内容相同，地址不同的String对象


<!--more-->


### String的比较

  "=="  比较地址
  "equals"  比较内容

  举例:
  String s1 = "hello";
  String s2 = "hello";
  String s3 = new String("hello");

  s1 == s2;   // true  地址相同
  s1 == s3;   // false  地址不同
  s1.equals(s2);  // true  内容相同
  s1.equals(s3);  // true  内容相同


### intern() 方法

  查找内容相同(equals())的字符串


     String s1 = "hello";   // hello不存在，jvm创建新对象 (1)
      String s2 = new String("hello");  // 创建新对象 (2)，这时heap中存在两个内容为hello的对象
      s1 == s2;  // false  // 地址不同
      s1.equals(s2);  // true  // 内容相同
      s2 = s2.intern();  // true  // 找到对象(1) 并赋予s2
      s1 == s2;  // true !!  // 注意：此时s1,s2同指向(1)

## 效率：String 与 StringBuffer

  情景1：
  (1) String result = "hello" + " world";
  (2) StringBuffer result = new String().append("hello").append(" world");

  (1) 的效率好于 (2)，不要奇怪，这是因为jvm会做如下处理
  编译前  String result = "hello" + " world";
  编译后  String result = "hello world";


  情景2：
  (1) public String getString(String s1, String s2) {
   return s1 + s2;
  }
  (2) public String getString(String s1, String s2) {
   return new StringBuffer().append(s1).append(s2);
  }

  (1) 的效率与 (2) 一样，这是因为jvm会做如下处理
  编译前  return s1 + s2;
  编译后  return new StringBuffer().append(s1).append(s2);

情景3：
  (1) String s = "s1";
   s += "s2";
   s += "s3";
  (2) StringBuffer s = new StringBuffer().append("s1").append("s2").append("s3");

  (2) 的效率好于(1)，因为String是不可变对象，每次"+="操作都会造成构造新的String对象


  情景4：
  (1) StringBuffer s = new StringBuffer();
  for (int i = 0; i < 50000; i ++) {
   s.append("hello");
  }
  (2) StringBuffer s = new StringBuffer(250000);
  for (int i = 0; i < 50000; i ++) {
   s.append("hello");
  }
 
  (2) 的效率好于 (1)，因为StringBuffer内部实现是char数组，默认初始化长度为16，每当字符串长度大于char 数组长度的时候，jvm会构造更大的新数组，并将原先的数组内容复制到新数组，(2)避免了复制数组的*开销*

 

**关键点**

  1). 简单的认为 .append() 效率好于 "+" 是错误的！
  2). 不要使用 new 创建 String
  3). 注意 .intern() 的使用
  4). 在编译期能够确定字符串值的情况下，使用"+"效率最高
  5). 避免使用 "+=" 来构造字符串
  6). 在声明StringBuffer对象的时候，指定合适的capacity，不要使用默认值(18)
  7). 注意以下二者的区别不一样
  - String s = "a" + "b";
  - String s = "a";
  s += "b";

========================================================================
## String和StringBuffer之概览
　　创建字符串的较佳途径
　　滞留字符串带来的优化
　　连接字符串时的优化技巧
　　借助StringBuffer的初始化过程的优化技巧
　　关键点

 

String和StringBuffer之概览
　　非可变对象一旦创建之后就不能再被改变，可变对象则可以在创建之后被改变。String对象是非可变对象，StringBuffer对象则是可变对象。为获得更佳的性能你需要根据实际情况小心谨慎地选择到底使用这两者中的某一个。下面的话题会作详细的阐述。（注意：这个章节假设读者已经具备Java的String和StringBuffer的相关基础知识。）

 

创建字符串的较佳途径

你可以按照以下方式创建字符串对象：
1. String s1 = "hello"; 
    String s2 = "hello"; 
2. String s3 = new String("hello");
    String s4 = new String("hello");

 

上面哪种方式会带来更好的性能呢？下面的代码片断用来测量二者之间的区别。


*StringTest1.java*

    package com.performance.string;
    
    public class StringTest1 {
    
    public static void main(String[] args){
    
        // create String literals
        long startTime = System.currentTimeMillis();
        for(int i=0;i<50000;i++){
    
        String s1 = "hello";
        String s2 = "hello";
        }
    
        long endTime = System.currentTimeMillis();
        System.out.println("Time taken for creation of String literals : "
                      + (endTime - startTime) + " milli seconds" );
    
        // create String objects using 'new' keyword       
        long startTime1 = System.currentTimeMillis();
        for(int i=0;i<50000;i++){
    
        String s3 = new String("hello");
        String s4 = new String("hello");
        }
    
    long endTime1 = System.currentTimeMillis();
    
     
    
    System.out.println("Time taken for creation of String objects : "
                      + (endTime1 - startTime1)+" milli seconds");
        }
    }

这段代码的输出：
Time taken for creation of String literals : 0 milli seconds

Time taken for creation of String objects : 170 milli seconds

 

## JVM是怎样处理字符串的呢？

　　Java虚拟机会维护一个内部的滞留字符串对象的列表（唯一字符串的池）来避免在堆内存中产生重复的String对象。当JVM从class文件里加载字符串字面量并执行的时候，它会先检查一下当前的字符串是否已经存在于滞留字符串列表，如果已经存在，那就不会再创建一个新的String对象而是将引用指向已经存在的String对象，JVM会在内部为字符串字面量作这种检查，但并不会为通过new关键字创建的String对象作这种检查。当然你可以明确地使用String.intern()方法强制JVM为通过new关键字创建的String对象作这样的检查。这样可以强制JVM检查内部列表而使用已有的String对象。

　　所以结论是，JVM会内在地为字符串字面量维护一些唯一的String对象，程序员不需要为字符串字面量而发愁，但是可能会被一些通过new关键字创建的String对象而困扰，不过他们可以使用intern()方法来避免在堆内存上创建重复的String对象来改善Java的运行性能。下一小节会向大家展示更多的信息。

 

下图展示了未使用intern()方法来创建字符串的情况。

　　你可以自己使用==操作符和String.equals()方法来编码测试上面提到的区别。==操作符会返回true如果一些引用指向一个相同的对象但不会判断String对象的内容是否相同；String.equals()方法会返回true如果被操作的String对象的内容相同。对于上面的代码会有s1==s2，因为s1和s2两个引用指向同一个对象，对于上面的代码，s3.equals(s4)会返回true因为两个对象的内容都一样为”hello”。你可以从上图看出这种机制。在这里有三个独立的包含了相同的内容（”hello”）的对象，实际上我们不需要这么三个独立的对象――因为要运行它们的话既浪费时间又浪费内存。

 
那么怎样才能确保String对象不会重复呢？下一个话题会涵盖对于内建String机制的兴趣。

 

滞留字符串的优化作用
　　同一个字符串对象被重复地创建是不必要的，String.intern()方法可以避免这种情况。下图说明了String.intern()方法是如何工作的，String.intern()方法检查字符串对象的存在性，如果需要的字符串对象已经存在，那么它会将引用指向已经存在的字符串对象而不是重新创建一个。下图描绘了使用了intern()方法的字符串字面量和字符串对象的创建情况。

 

下面的例程帮助大家了解String.intern()方法的重要性。
*StringTest2.java*

 

    package com.performance.String;
    
    // This class shows the use of intern() method to improve performance
    
    public class StringTest2 {
    
    public static void main(String[] args){
    
        // create String references like s1,s2,s3...so on..
        String variables[] = new String[50000];
        for( int i=0;i<variables.length;i++){
            variables[i] = "s"+i;
        }
    
        // create String literals
        long startTime0 = System.currentTimeMillis();
        for(int i=0;i<variables.length;i++){
            variables[i] = "hello";
        }
    
        long endTime0 = System.currentTimeMillis();
        System.out.println("Time taken for creation of String literals : "
                             + (endTime0 - startTime0) + " milli seconds" );
    
        // create String objects using 'new' keyword       
    
        long startTime1 = System.currentTimeMillis();
        for(int i=0;i<variables.length;i++){
            variables[i] = new String("hello");
        }
    
        long endTime1 = System.currentTimeMillis();
        System.out.println("Time taken for creation of String objects with 'new' key word : "
                            + (endTime1 - startTime1)+" milli seconds");
    
        // intern String objects with intern() method   
        long startTime2 = System.currentTimeMillis();
        for(int i=0;i<variables.length;i++){
    
            variables[i] = new String("hello");
            variables[i] = variables[i].intern();
        }
    
        long endTime2 = System.currentTimeMillis();
        System.out.println("Time taken for creation of String objects with intern(): "
                            + (endTime2 - startTime2)+" milli seconds");
        }
    }

这是上面那段代码的输出结果：
Time taken for creation of String literals : 0 milli seconds
Time taken for creation of String objects with 'new' key word : 160 milli seconds
Time taken for creation of String objects with intern(): 60 milli seconds

 

连接字符串时候的优化技巧
　　你可以使用+操作符或者String.concat()或者StringBuffer.append()等办法来连接多个字符串，那一种办法具有最佳的性能呢？

　　如何作出选择取决于两种情景，第一种情景是需要连接的字符串是在编译期决定的还是在运行期决定的，第二种情景是你使用的是StringBuffer还是String。通常程序员会认为StringBuffer.append()方法会优于+操作符或String.concat()方法，但是在一些特定的情况下这个假想是不成立的。

 

1) 第一种情景：编译期决定相对于运行期决定
请看下面的StringTest3.java代码和输出结果。


    package com.performance.String;
    
    
    public class StringTest3 {
    
      public static void main(String[] args){
        //Test the String Concatination
        long startTime = System.currentTimeMillis();
        for(int i=0;i<5000;i++){
        String result = "This is"+ "testing the"+ "difference"+ "between"+
                "String"+ "and"+ "StringBuffer";
    
        }
    
        long endTime = System.currentTimeMillis();
        System.out.println("Time taken for String concatenation using + operator : "
    
        + (endTime - startTime)+ " milli seconds");
    
        //Test the StringBuffer Concatination
        long startTime1 = System.currentTimeMillis();
        for(int i=0;i<5000;i++){
        StringBuffer result = new StringBuffer();
             result.append("This is");
            result.append("testing the");
            result.append("difference");
            result.append("between");
           result.append("String");
           result.append("and");
           result.append("StringBuffer");
         }
    
        long endTime1 = System.currentTimeMillis();
        System.out.println("Time taken for String concatenation using StringBuffer : "
               + (endTime1 - startTime1)+ " milli seconds");
      }
    }

这是上面的代码的输出结果：
Time taken for String concatenation using + operator : 0 milli seconds
Time taken for String concatenation using StringBuffer : 50 milli seconds
很有趣地，+操作符居然比StringBuffer.append()方法要快，为什么呢？

 

　　这里编译器的优化起了关键作用，编译器像下面举例的那样简单地在编译期连接多个字符串。它使用编译期决定取代运行期决定，在你使用new关键字来创建String对象的时候也是如此。

 

编译前：
String result = "This is"+"testing the"+"difference"+"between"+"String"+"and"+"StringBuffer";
编译后：
String result = "This is testing the difference between String and StringBuffer";


这里String对象在编译期就决定了而StringBuffer对象是在运行期决定的。运行期决定需要额外的开销当字符串的值无法预先知道的时候，编译期决定作用于字符串的值可以预先知道的时候，下面是一个例子。

 

编译前：
public String getString(String str1,String str2) {
    return str1+str2;
}
编译后：
return new StringBuffer().append(str1).append(str2).toString();

运行期决定需要更多的时间来运行。

 

2) 第二种情景：使用StringBuffer取代String
看看下面的代码你会发现与情景一相反的结果――连接多个字符串的时候StringBuffer要比String快。
StringTest4.java

 

    package com.performance.String;
    
    
    public class StringTest4 {
    
     public static void main(String[] args){
    
        //Test the String Concatenation using + operator
        long startTime = System.currentTimeMillis();
        String result = "hello";
        for(int i=0;i<1500;i++){
            result += "hello";
        }
    
        long endTime = System.currentTimeMillis();
        System.out.println("Time taken for String concatenation using + operator : "
                      + (endTime - startTime)+ " milli seconds");
    
        //Test the String Concatenation using StringBuffer
        long startTime1 = System.currentTimeMillis();
        StringBuffer result1 = new StringBuffer("hello");
        for(int i=0;i<1500;i++){
            result1.append("hello");
        }
    
        long endTime1 = System.currentTimeMillis();
        System.out.println("Time taken for String concatenation using StringBuffer :  "
                      + (endTime1 - startTime1)+ " milli seconds");
        }
    }

这是上面的代码的输出结果：
Time taken for String concatenation using + operator : 280 milli seconds
Time taken for String concatenation using StringBuffer : 0 milli seconds

看得出StringBuffer.append()方法要比+操作符要快得多，为什么呢？

 

借助StringBuffer的初始化过程的优化技巧
　　你可以通过StringBuffer的构造函数来设定它的初始化容量，这样可以明显地提升性能。这里提到的构造函数是StringBuffer(int length)，length参数表示当前的StringBuffer能保持的字符数量。你也可以使用ensureCapacity(int minimumcapacity)方法在StringBuffer对象创建之后设置它的容量。首先我们看看StringBuffer的缺省行为，然后再找出一条更好的提升性能的途径。

 

StringBuffer的缺省行为：
　　StringBuffer在内部维护一个字符数组，当你使用缺省的构造函数来创建StringBuffer对象的时候，因为没有设置初始化字符长度，StringBuffer的容量被初始化为16个字符，也就是说缺省容量就是16个字符。当StringBuffer达到最大容量的时候，它会将自身容量增加到当前的2倍再加2，也就是（2*旧值+2）。

　　如果你使用缺省值，初始化之后接着往里面追加字符，在你追加到第16个字符的时候它会将容量增加到34（2*16+2），当追加到34个字符的时候就会将容量增加到70（2*34+2）。无论何时只要StringBuffer到达它的最大容量它就不得不创建一个新的字符数组然后重新将旧字符和新字符都拷贝一遍――这也太昂贵了点。所以总是给StringBuffer设置一个合理的初始化容量值是错不了的，这样会带来立竿见影的性能增益。

　　我利用两个StringBuffer重新测试了上面的StringTest4.java代码，一个未使用初始化容量值而另一个使用了。这次我追加了50000个’hello’对象没有使用+操作符。区别是我使用StringBuffer(250000)的构造函数来初始化第二个StringBuffer了。

 

输出结果如下：
Time taken for String concatenation using StringBuffer with out setting size: 280 milli seconds

Time taken for String concatenation using StringBuffer with setting size: 0 milli seconds

StringBuffer初始化过程的调整的作用由此可见一斑。所以，使用一个合适的容量值来初始化StringBuffer永远都是一个最佳的建议。

 

关键点
1. 无论何时只要可能的话使用字符串字面量来常见字符串而不是使用new关键字来创建字符串。
2. 无论何时当你要使用new关键字来创建很多内容重复的字符串的话，请使用String.intern()方法。
3. +操作符会为字符串连接提供最佳的性能――当字符串是在编译期决定的时候。
4. 如果字符串在运行期决定，使用一个合适的初期容量值初始化的StringBuffer会为字符串连接提供最佳的性能。

反馈
原文作者：commentsZZZ@precisejavaZZZ.com
中文翻译：lamp5w@yahoo.com.cn

 

译注：本文涉及较多的Java底层知识，如有不清楚的地方请参阅《Java语言规范》和《Java虚拟机规范》的相关章节，你可以在 http://java.sun.com 找到。本翻译过程力求准确严谨忠实原文，然因为语言习惯不一样，部分字句乃是意译，如有疑问请参考原文《Performance improvement techniques in String and StringBuffer》，原文地址：http://www.precisejava.com/javaperf/j2se/StringAndStringBuffer.htm 。同时，因为本人知识浅薄，不当之处在所难免，欢迎大家批评指正。 