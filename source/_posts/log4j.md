---
title: log4j
date: 2011-09-18 22:33:00
updated: 2015-09-19 10:34:45
tags: 
categories: 
- java

---
## 1.log4j有三种主要组件：
logger、appender and layout            

## 2.Log4j提供的appender有以下几种：            
　org.apache.log4j.ConsoleAppender（控制台)  
　org.apache.log4j.FileAppender（文件)            
　org.apache.log4j.DailyRollingFileAppender（每天产生一个日志文件）        
　org.apache.log4j.RollingFileAppender（文件大小到达指定尺寸的时候产生一个新的文件）  
　org.apache.log4j.WriterAppender（将日志信息以流格式发送到任意指定的地方）        

## 3.Log4j提供的layout有以下几种：            
　org.apache.log4j.HTMLLayout（以HTML表格形式布局)          
　org.apache.log4j.PatternLayout（可以灵活地指定布局模式）        
　org.apache.log4j.SimpleLayout（包含日志信息的级别和信息字符串）        
　org.apache.log4j.TTCCLayout（包含日志产生的时间、线程、类别等等信息）        


<!--more-->


## 4.Log4j提供的几种输出格式：            
　％M：Used to output the method name where the logging request was issued.  
　％m：Used to output the application supplied message associated with the logging event.  
　％l：Used to output location information of the caller which generated the logging event  
　％L：Used to output the line number from where the logging request was issued.  
　％p：Used to output the priority of the logging event.          
　％n：Outputs the platform dependent line separator character or characters.      
　 ％r：Used to output the number of milliseconds elapsed since the start of the application 　　　until the creation of the logging event.
　％F：Used to output the file name where the logging request was issued.    
　％d：Used to output the date of the logging event.          
　％c：Used to output the category of the logging event          
　％C：Used to output the fully qualified class name of the caller issuing the logging request

## 5.log.isDebugEnabled()
  如果是对于效率要求比较高的话，要在log.debug()之前加上log.isDebugEnabled()进行判断，　这样能够大大减少执行时间

## 6.对于各个appenders,共有的属性是layout（一般设置为　　org.apache.log4j.PatternLayout）,Threshold(Log的级别)
　(1)ConsoleAppender:Target(System.out和System.err)            
　(2)FileAppender:File(定义输出的文件名），Append（定义是否为追加）        
　(3）DailyRollingFileAppender（除FileAppender属性外）:MaxFileSize（最大文件大小），　　MaxBackupIndex（最大备份文件个数）

## 7.log4j中有五级logger

    #FATAL 0
    #ERROR 3
    #WARN 4
    #INFO 6
    #DEBUG 7

## 8.配置根Logger，其语法为：
log4j.rootLogger = [ level ] , appenderName, appenderName, …
log4j.rootLogger=INFO, A1 ,R
这一句设置以为着所有的log都输出
如果为log4j.rootLogger=WARN, 则意味着只有WARN,ERROR,FATAL
被输出，DEBUG,INFO将被屏蔽掉.

## 9.日志信息格式中几个符号所代表的含义：
log4j.appender.A1.layout.ConversionPattern=%-4r %-5p %d{yyyy-MM-dd HH:mm:ssS} %c %m%n

　　－X号: X信息输出时左对齐；
　　%p: 日志信息级别
　　%d{}: 日志信息产生时间
　　%c: 日志信息所在地（类名）
　　%m: 产生的日志具体信息
　　%n: 输出日志信息换行