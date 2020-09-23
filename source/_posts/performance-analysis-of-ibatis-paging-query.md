---
title: iBatis分页查询的性能问题分析
date: 2010-06-02 10:41:00
updated: 2016-06-02 11:22:15
tags: 
- mysql
- optimize
- groupby
categories: 
- sql

---
## 1.insert,update,delete 返回值
(1).insert 返回的为插入的主键值，但必须在配置文件中加入<selectKey/>
如果主键值为String
```
    <selectKey resultClass="string" keyProperty="id">
       SELECT @@IDENTITY AS ID
    </selectKey>
```
如果主键值为Int型
```
<selectKey resultClass="java.lang.Integer" keyProperty="id" >
   SELECT @@IDENTITY AS ID
</selectKey>
``` 


<!--more-->


或者
```
<selectKey resultClass="int" keyProperty="id">
   SELECT @@IDENTITY AS ID
</selectKey>
```
**注**：@@IDENTITY只是SQL Server的写法，其他数据库也有相关的属性。

(2).Update,和Delete返回为修改数据影响的条数；
## 2.SQL模糊查询的两种方法
(1).如果是模糊查询，在关键字传入前加‘%’； 例：String keyword=”%”+keyword+”%”;
(2).配置文件的写法： select name,sex from t_user where name like '%$name$%' 
## 3.在ibatis的bean.xml配置文件中，日期的转换
```
<![CDATA[
select count( 1 ) from dbu_window where   
(     
( DATE_FORMAT( '$dateStartTemp$', '%Y-%m-%d' ) >= f_start_date and
    DATE_FORMAT( '$dateStartTemp$', '%Y-%m-%d' ) <= f_end_date )
or     
( DATE_FORMAT( '$dateEndTemp$', '%Y-%m-%d' ) >= f_start_date and 
    DATE_FORMAT( '$dateEndTemp$', '%Y-%m-%d' ) <= f_end_date ) )
]]>

```
## 4.$sql$即程序预定义sql语句的应用

java代码：
```
String selSql = "select count(*) from `bbs_config`;";
sqlMapClient.queryForObject( "execSelSqlStr" , selSql );
```
bean.xml文件：
```
<select id="execSelSqlStr" resultClass="int" parameterClass="string">
   <![CDATA[   $sql$   ]]>
</select>
```
## 5.ResultClass和ResultMap的区别

resultClass:可以把结果自动映射到相应的JAVA类中，但无法指定输出字段的类型，会对性能产生轻微的影响。
resultMap:可以预先定义resultMap元素，可以控制数据如何从结果集中取出，以及哪个属性匹配哪个字段，

parameterClass和parameterMap的区别
parameterClass属性值是JAVA类的全限定名，目的是限制输入参数的类型为指定的JAVA类，如果不指定，任何带有合适属性（get/set方法）的 `JAVA BEAN` 都可以作为输入参数
parameterMap:预先定义parameterMap的属性值，用于有次序的参数匹配JDBC符号

## 6.“#”和“$”的区别

“#” 为占为符，#在是用prepareStatement的?插入参数,$是文本替换
“$”为直接替换，但为出现SQL引入问题和性能上的影响，like,limit和表名的引入必须使用“$”
## 7.where 条件的组装

<isEqual>         比较属性值和静态值或另一个属性值是否相等
<idNotEqual>      比较属性值和静态值或另一个属性值是否不相等
<isGreaterThan>    比较属性值是否大于静态值或另一个属性值
<isGreaterEqual>   比较属性值是否大于等于静态值或另一个属性值
<isLessThan>      比较属性值是否小于静态值或另一个属性值
<isLessEqual>     比较属性值是否小于等于静态值或另一个属性值
<isNull>          检查属性是否为NULL
<isNotNull>       检查属性是否不为NULL
<isEmpty>        检查属性是否为NULL或空
<isNotEmpty>     检查属性是否不为NULL或不为空
<isPropertyAvailable>       检查是否存在该属性
<isNotPropertyAvailable>    检查是否不存在该属性
## 8.<![CDATA[ ]]>的含义
用了特殊字符的SQL语句(比如>，<)不能直接使用。必须用<![CDATA[ ]]>
## 9.ibatis自带缓存的运用
```
<cacheModel id=”person-cache” implementation=”LRU”>
   <flushInterval hours=”24”/>       //可以用hours/minutes/second
   <flushOnExecute   statement=”selectperson”/>    //需要缓存的方法，可以为多个
   <property name=”size” value=”1000”/>
</cacheModel>
```
缓存策略：
   LRU：最近最少使用
   MEMORY：适用于没有统一的对象重用模式，property的属性值必须是STRONG，
   SOFT，WEAK，这三个值分别对应于JVM不同内存reference类型。
   WEAK,大多数情况下，WEAK是最佳选择，缺省值就是WEAK，它能大大提高常用查询的性能，对于当前不被使用的查询数据，将被清除
   SOFT，在查询结果对象不被使用，可以减少内存不足的可能性
   STRONG，对查询的结果一直保留在内存中，可以使用在数据量很少或者静态数据的时
   FIFO：先进先出
   OSCACHE：配置oscache.properties

## 10.value的使用

假如没有必要写JAVA BEAN作为参数，可以直接使用基本类型，也可以直接传入参数值。如果是直接传入参数值就配置文件中必须写 #value#做为参数传入

## 11.事务的处理
```
startTransacton();
commitTransaction();
rollbackTransaction();
```
批处理
如果有很多非查询的（insert/update/delete）语句要处理，为了减少网络通讯的流量，可以进行批处理
```
sqlMaClientp.startBatch();
sqlMapClient.endBatch();
```
## 12.iterate在ibatis里的运用，List参数名后面一定要加“[]”，防止解析器简单的把List解析成String
```
<select id="getUserInfoByName" resultMap="userResult" parameterClass="java.util.List">
select * from tuserinfo where f_user_code in
    <iterate open="(" close=")" conjunction="," >
<![CDATA[
           #value[]# 
        ]]>
   </iterate>
</select>
```
13.xmlResultName的运用 详细输出结果，直接把查存出来的结果映射成XML document
```   
<select id="getUser" parameterClass="user" resultClass="xml" xmlResultName="person">
    <![CDATA[ 
        select name,sex from t_user where id = #id# 
     ]]>
</select>
String xml= (String)sqlMapClient.queryForObject("getUser",user );
System.out.println(xml);

<?xml version="1.0" encoding="UTF-8"?>
   <person>
      <name>we are good !</name>
      <sex>2</sex>
   </person>
```
## 14.分页功能的实现
1). 用ibatis自带的分页功能，但是自带分页是把所有数据读入内存再分页的，所以必须自己覆写LimitSqlExecutor方法，
2). 直接使用limit实现
## 15.ibatis的IDE：http://ibatis.apache.org/ibator.html
## 16.容易出错的地方 :
1) 在parameterMap和resultMap中，字段数据类型是java.sql.Types类定义的常量名称。常用的数据类型包括 BLOB，CHAR，CLOB，DATE，LONGVARBINARY， INTEGER，NULL，NUMERIC，TIME，TIMESTAMP和VARCHAR等。 
2) 对于数据表中NULLBALE的字段，必须在parameterMap和resultMap中指定字段的数据类型。 
3) 对于数据类型是DATE，CLOB或BLOB的字段，最好在parameterMap和resultMap中指定数据类型。 
4) 对于二进制类型的数据，可以将LONGVARBINARY映射成byte[]。 
5) 对于文本类型较大的数据，可以将CLOB映射成String。 
6) Java Bean必须拥有缺省的构造器（即无参数的构造器）。 
7) Java Bean最好实现Serializable接口，以备应用的进一步扩展。

## 17.曾经写了一个ibatis动态查询的例子：
排序代码如下：
```
<dynamic prepend=" order by">
<isNotNull property="sortName">
   <isNotEqual property="sortName" compareValue="">
    <![CDATA[#sortName# ]]>
   </isNotEqual>
</isNotNull>
<isNotNull property="sortSequence">
   <isNotEqual property="sortSequence" compareValue="">
    <![CDATA[ #sortSequence# ]]>
   </isNotEqual>
</isNotNull>
</dynamic>
```
竟然无法排序，后来查了下资料，把“#”改成了“$”就可以了。
现总结下经验：#xxx# *xxx代表是属性值*,map里面的key或者是你的pojo对象里面的属性,ibatis会自动在它的外面加上引号,表现在sql语句是这样的 where xxx = 'xxx' ;而$xxxx$ 则是把xxxx作*为字符串拼接到你的sql语句中*,比如 order by topicId , 如果你不用$来拼接而用#的话,外面就会被加上引号，比如你的语句这样写 ... order by #xxx# (xxx就是你传进来的字符串topicId),ibatis 就会把他翻译成 order by 'topicId' 这样就报错了，用$的结果就是这样 order by topicId。

## 18.ibatis通用sql语句可以单独定义如：
```
<sql id="whereTemplate">
<dynamic prepend="where">
   <isNotNull prepend="and" property="id">
    <![CDATA[ id = #id# ]]>
   </isNotNull>
</dynamic>
</sql>
<select id="coreGetCountAll" resultClass="java.lang.Integer" parameterClass="coreNote">
<![CDATA[ SELECT COUNT( 1 ) FROM note ]]>
<include refid="whereNoteTemplate" />
</select>
```
由于iBatis从2.3.4版本后就改为MyIBatis了，为了研究源码方便，特上传2版本的源码和发布包。

其他文章 [兼顾效率，iBatis一些非见用法(10条)](https://yigrherb.com/2010/05/05/taking-into-account-the-efficiency-some-of-the-non-ibatis-usage-10.html)
原文: [http://www.cnblogs.com/zqmingok/articles/1713500.html](http://www.cnblogs.com/zqmingok/articles/1713500.html)
