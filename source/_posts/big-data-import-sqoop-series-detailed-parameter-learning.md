---
title: 大数据学习系列之 Sqoop导入参数详解
date: 2016-11-30 12:32:00
updated: 2016-12-11 10:28:17
tags: 
- javascript
- load
- modules
categories: 
- java

---
## 通用参数

 - `–connect`，数据库连接字符串
 - `–username`，数据库访问用户名
 - `–password` ，指定数据库连接密码（明文）
 - `-P` 交互式的指定数据库密码
 - `–password-file`，使用密码文件制定数据库密码

## 导入控制参数——选择部分数据导入

 - `–query`，要导入的数据用SQL查询控制

示例：
```bash
sqoop import –connect jdbc:mysql://bigdata:3306/sqooptest –username root –password 123456 –query "select * from sqoop where student_id<=500 AND $CONDITION"
```
 - `–table`，指定要导入的数据表 tbl 、–where，指定导入时的条件 “student_id<=500”
 - `–columns`，指定要导入的字段名 例如 –columns class_id,class_name

## 目的目录（HDFS）


<!--more-->


 - `–warehouse-dir`，指定导入到HDFS中的目录

## 分隔符

 - `–fields-terminated-by`，例`– fields-terminated-by "|"，默认情况下用","进行字段分隔
 - `–lines-terminated-by`,行分隔符
 - `–escaped-by`，转义字符

## 控制导入并行度

`-m`或`–num-mappers [INT]`，控制导入时MR作业的Map任务数量，后接一个整数值，用来表示MR的并行度。在进行并行导入的时候，Sqoop会使用`split-by`进行负载切分（按照表的PK进行切分），首先获取切分字段`Max`和`Min`值，再根据Max和Min值去进行切分，举例：`student_id [1,1000]`，Sqoop会直接使用4个Map任务`select * from student where id >=min and id <=max，(1,250),(250,500),(500,750),(750,1000)`。如果非常不幸，你的ID或切分字段不是均匀分布的话，会导致任务的不平衡。*注明*：Sqoop目前不能使用多个字段作为切分字段。

## 类型映射(导入到Hive时使用)

```bash
–map-column-hive， id=String,value=Int
```
## 实战

### 查看对应库、表情况
```bash
sqoop list-databases –connect jdbc:mysql://bigdata:3306 –username root –password 123456 
sqoop list-tables –connect jdbc:mysql://bigdata:3306/sqooptest –username root –password 123456
```
###  密码

`–password`，明文密码 
`-P`，交互式密码 
`–password-file`， 失败【待解决】

### 导入全表
```bash
sqoop import --connect jdbc:mysql://bigdata:3306/sqooptest --username root --password 123456 --table bigdata
```
注意: 

 1. 目录不能已存在
 2. 没指定-m或splite-by时，表必须有PK

### 指定导入目录
```bash
sqoop import --connect jdbc:mysql://bigdata:3306/sqooptest --username root --password 123456 --table bigdata --warehouse-dir /sqoopim
```
### 控制并行度
```bash
sqoop import --connect jdbc:mysql://bigdata:3306/sqooptest --username root --password 123456 --table bigdata -m 2
```
### 控制字段分隔符
```bash
sqoop import --connect jdbc:mysql://bigdata:3306/sqooptest --username root --password 123456 --table bigdata -m 2 –fields-terminated-by "|"
```
### 导入部分数据

仅导入class_id<=5 的数据 
`–query`，必须使用`–target-dir`指定导入目录，且删除`–table`参数
```bash
sqoop import --connect jdbc:mysql://bigdata:3306/sqooptest --username root --password 123456 --query "select * from bigdata where class_id<=5 and \$CONDITIONS" -m 1 --target-dir /user/root/bigdata
```
–where，直接用–where指定条件，用引号引起来
```bash
sqoop import --connect jdbc:mysql://bigdata:3306/sqooptest --username root --password 123456 --table bigdata --where "class_id<=5" -m 1
```
–columns，指定导入字段名 
仅导入class_id,class_name,teacher
```
sqoop import --connect jdbc:mysql://bigdata:3306/sqooptest --username root --password 123456 --table bigdata --columns class_id,class_name,teacher -m 1
```
### 使用文件进行导入

注意：文件中的参数与指定值必须每个一行 
写入参数文件`sqoop.imp`，内容如下：
```
import
--connect
jdbc:mysql://centos:3306/sqooptest
--username
root
--password
123456
--table
bigdata
--columns
class_id,class_name,teacher
-m
1
```
执行sqoop：
```
sqoop --options-file sqoop.imp
```

## 导入数据时添加时间戳字段

> 背景说明：当利用`Sqoop`从`Oracle`等关系型数据库中抽取数据到`Hive`或`HDFS`时，加载到Hive或HDFS的数据需要有一个加载时间戳，以便于后续加载的数据做更新比对。

*解决思路*：

在Sqoop中进行导入时，可以使用–columns来指定导入RDB中表的某些固定字段，试验后发现这种方式在表的字段数较少时是可行的，通过 –columns c1,c2,sysdate来指定，但是当表的字段达到几十个、上百个时，就不是一个好的方法的；
上路不通继续寻找，在Sqoop中还可以使用`–query ‘select * from tb where $CONDITIONS’`来导入，并且可以指定约束条件，经验证此法可行，Action！

### 原来的导入
```bash
sqoop import --connect jdbc:oracle:thin:@//oracle:1521/pdbORCL --username mars --password-file mars.pwd --table TBL --null-string '\\N' --null-non-string '\\N' --target-dir /user/hive/warehouse/mars/TBL -m 1
```
这是全表导入，导入的数据与在Oracle中表的数据是一模一样的，包括表结构和数据

### 添加时间戳字段的导入

获取当前时间作为Hive或HDFS中的加载时间戳
```bash
sqoop import --connect jdbc:oracle:thin:@//oracle:1521/pdbORCL --username mars --password-file mars.pwd --query "select TBL.*,sysdate from TBL where id > 5 AND \$CONDITIONS" --null-string '\\N' --null-non-string '\\N' --target-dir /user/hive/warehouse/mars/TBL -m 1
```
*几点注意*：

 - `–query`后跟的查询语句可以是单引号引起来，也可以是双引号，无论何种引号都必须加上`$CONDITIONS`；但当用双引号引用时，必须在$CONDITIONS前加上反斜杠即`\$CONDITIONS`；
 - 查询中的select语句要按照对应RDB的语法，例如本例中是Oracle的语法去写的，获取当前系统时间作为时间戳，在使用Oracle中的sysdate函数