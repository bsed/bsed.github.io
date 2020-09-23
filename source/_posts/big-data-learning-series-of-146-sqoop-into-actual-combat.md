---
title: 大数据学习系列之 Sqoop 1.4.6 导入实战
date: 2016-12-05 12:22:00
updated: 2016-12-11 12:35:29
tags: 
- javascript
- prototype
categories: 
- js

---
> `Sqoop`是`Hadoop`与其他关系型数据库之间进行数据抽取、转换的一座坚固的桥梁，可以在关系型数据库以及Hadoop生态系统支持的数据存储方式（HDFS、Hive、HBase）之间进行导入和导出操作。

*注意几点:* 
1. `incremental append`模式不支持hive导入至hive 
2. `Sqoop命`令中的表名及字段名等是大小写敏感的

## 1. 安装使用

解压Sqoop二进制包并配置好环境变量
```
sqoop-1.4.6.jar
```
第一步先将`sqoop jar`包拷贝至`Hadoop`的`mapreduce`目录下：
```bash
cp $SQOOP_HOME/sqoop-1.4.6.jar $HADOOP/share/hadoop/mapreduce/
```
## 2. JDBC jar包

第二步将`Oracle`和`MySQL`分别需要用到的jar包拷贝至`$SQOOP_HOME/lib`下
```
cp ojdbc6.jar $SQOOP_HOME/lib/
cp mysql-connector-java-5.1.38-bin.jar $SQOOP_HOME/lib/
```
## 2. 导入

### 1. 从RDB导入表数据至HDFS
#### 1. Oracle


<!--more-->


从Oracle数据库的Schema为pdbORCL中导入表PUB_DATE_D
```bash
sqoop import --connect jdbc:oracle:thin:@//oracle:1521/pdbORCL --username DM -password 123456 --table PUB_DATE_D --warehouse-dir /user/root/oracletest -m 1
```
此时会在`/user/root/`下生成目录为`oracletest`的目录，且表中的数据均在这个目录下，字段之间以逗号（，）作为分隔符。

#### 2. MySQL

如果不指定目录，默认文件导入到`/user/root`下，指定目录用`–warehouse-dir`或`–target-dir`
``bash
sqoop import --connect jdbc:mysql://mysql:3306/taobao --username root --password 123456 --table we --warehouse-dir /mytargetdir -m 1
```
### 2. 从RDB导入表数据至Hive

#### 1. Oracle

从Oracle中导入表数据至Hive中的default数据库，并直接创建该表（即原本此表结构在hive中是不存在的）
```
sqoop import --connect jdbc:oracle:thin:@//oracle:1521/pdbORCL --username DM -password 123456 --table PUB_DATE_D --hive-import --hive-database default --create-hive-table -m 1
```
通过这种方式导入的数据在hive中生成的数据内容（HDFS文件）以hive中的默认分隔符进行字段分隔；

#### 2. MySQL

当表在Hive不存在时，添加–create-hive-table则在指定数据库中添加同名表
```bash
sqoop import --connect jdbc:mysql://mysql:3306/taobao --username root --password 123456 --table we --hive-import --hive-database default --create-hive-table -m 1
```
#### 3. 导入全部表

如果要导入某一个数据库的所有表，可以直接执行一次命令即可，如下：
```bash
sqoop import-all-tables --connect jdbc:oracle:thin:@//oracle:1521/pdbORCL --username DM --password-file sqoop.pwd --hive-import --hive-database oracle -m 1
```
如果在导入所有表中的绝大多数表，则可以用参数–exclude-tables排除不需要导入的表（多表以逗号分隔）即可。

#### 4. 空值的处理

默认情况下，如果RDB中存在空值，则导入时用字符串常量null（小写）代替所有空值。此种处理方式并不符合大多数的空值处理要求。而Sqoop提供了2个参数以供处理空值： 
其中null-string用于处理数据库中文本类型的字段，null-non-string用于处理非文本类型的字段

sqoop import --connect jdbc:oracle:thin:@//myoracle:1521/pdbORCL --username DM --password-file sqoop.pwd --table PUB_DATE_D --where "QUARTER_NAME<'Q2'" --hive-import --hive-database default --null-string '\\N' --null-non-string '\\N'
## 3. 增量导入

### 1. 导入新数据

RDB中的数据库表中的数据仅附加，不做修改（append模式），需要有一个检查字段，和此字段中导入的最后值，则利用以下3个参数进行增量导入，导入id字段中值大于2的数据记录：（这种方式不能导入已有数据的更新记录），每次导入会打印出这3个参数的值。 
导入至Hive暂时不支持append模式，所以需要使用导入至HDFS的方式进行。
```
--incremental append
--check-column id
--last-value 2
```
具体示例：
```
sqoop import --connect jdbc:oracle:thin:@//oracle:1521/pdbORCL --username DM --password-file sqoop.pwd --table WE --warehouse-dir /user/hive/warehouse/ --null-string '\\N' --null-non-string '\\N' --incremental append --check-column ID --last-value 4
```
### 2. 导入更新数据

导入更新数据时使用lastmodified模式，每次导入会打印出这3个参数的值。这种模式要求标识字段必须是日期值（适合类型有date,time,datetime,timestamp）
```
--incremental lastmodified
--check-column last_update_date
--last-value "2016-04-14 19:11:20"
```
更新数据是指在数据表中发生变化的数据，指定一个标识字段，并指定一个更新值，当目的路径已存在该目录时，需要添加一个`–append`或`–merge-key`。

下列命令将导出表WE中字段LT的值>’2016-04-13 19:11:10’且<’当前系统时间’的所有数据记录，不同之处在于–append参数表示根据更新要求直接将抽取出来的数据附加至目标目录，而–merge-key参数则表示此更新操作将分成2个MR Job，Job1将表中的更新数据导入到HDFS中一个临时目录下，Job2将结合新数据和已经存在HDFS上的旧数据按照merge-key指定的字段进行合并（类似于去重），并输出到目标目录，每一行仅保留最新的值。 
此模式支持导入至Hive表，但是即便使用了merge-key参数也无法使新数据和旧数据进行合并去重 
*示例1：*
```bash
sqoop import --connect jdbc:oracle:thin:@//oarcle:1521/pdbORCL --username DM --password-file sqoop.pwd --table WE --warehouse-dir /user/hive/warehouse/ --null-string '\\N' --null-non-string '\\N' --incremental lastmodified --check-column LT --last-value '2016-04-13 19:11:10' --append
```
*示例2：*
```bash
sqoop import --connect jdbc:oracle:thin:@//oracle:1521/pdbORCL --username DM --password-file sqoop.pwd --table WE --warehouse-dir /user/hive/warehouse/ --null-string '\\N' --null-non-string '\\N' --incremental lastmodified --check-column LT --last-value '2016-04-13 19:11:10' --merge-key ID
```
### 3. 导入数据表的子集（可看做增量导入）

下列实例仅导入`QUARTER_NAM`E为`Q3`和`Q4`的数据（按天增量时，可考虑按照某个日期字段或时间字段取子集）
```bash
sqoop import --connect jdbc:oracle:thin:@//oracle:1521/pdbORCL --username DM -password research --table PUB_DATE_D --where "QUARTER_NAME>='Q3'" --hive-import --hive-database default
```
### 4. 保存上次导入的值

无论incremental是append模式还是lastmodified模式，都需要指定标识字段和上次更新值，如果手动写命令更新导入，则需要记录每一次的导入后打印的值，如下：
```bash
16/04/12 19:25:10 INFO tool.ImportTool:  --incremental append
16/04/12 19:25:10 INFO tool.ImportTool:   --check-column ID
16/04/12 19:25:10 INFO tool.ImportTool:   --last-value 5
```
可以利用sqoop metastore保存导入导出的参数及其值

**创建sqoop job**
```bash
sqoop job --create test -- import --connect jdbc:oracle:thin:@//oracle:1521/pdbORCL --username DM --password-file sqoop.pwd --table WE --hive-import --hive-table we1 --incremental lastmodified --check-column LT --last-value '2016-04-13 19:11:10' --merge-key ID
```
**执行sqoop job**
```bash
sqoop job --exec test
```
每次执行`sqoop job test，sqoop metastore`会保存此次`job`中`last-value`的最新值（即每次执行的系统时间），无需手动人工记住，以便于自动增量导入。

增量导入总结

Sqoop 1.4.6支持的增量导入方式只有append附加新数据记录和lastmodified新增修改数据两种，其中append不支持导入至Hive，而lastmodifed支持导入至Hive。
在自动化导入中，务必使用sqoop job的方式，在crontab中写入，并导出日志信息。
## 3. 密码保护（非命令行密码显示输入）

在上述的所有示例中是直接用password参数指定密码，可以通过以下两种方式进行密码保护。

`-P` 这种方式是使用-P参数替代`-password`，但这种方式在运行命令之后必须手动输入密码。
`–password-file filename` 
这种方式是事先将密码写入文件filename（此文件必须在HDFS上），然后通过–password-file进行读取 
先创建文件并上传到HDFS中，并将读写权限设置为400：
```bash
echo 'research' > sqoop.pwd
hdfs dfs -put sqoop.pwd /user/nanyue/
hdfs dfs -chmod 400 /user/nanyue/sqoop.pwd
rm -rf sqoop.pwd
```
导入测试
```bash
sqoop import --connect jdbc:oracle:thin:@//oracle:1521/pdbORCL --username DM --password-file sqoop.pwd --table PUB_DATE_D --where "QUARTER_NAME<'Q2'" --hive-import --hive-database default
```
### 4. 压缩

sqoop默认支持压缩，其默认的压缩格式是gzip，直接在sqoop import命令末尾添加–compress即可执行默认压缩，也可以使用–compression-codec参数指定压缩格式，例如下面示例为指定bzip2压缩格式。
```bash
sqoop import --connect jdbc:oracle:thin:@//oracle:1521/pdbORCL --username DM --password-file sqoop.pwd --table PUB_DATE_D --where "QUARTER_NAME<'Q2'" --hive-import --hive-database default --compress --compression-codec org.apache.hadoop.io.compress.BZip2Codec
```
### 5. 关于Hive版本

上述操作在以下版本中都可以顺利执行：

Hadoop 2.7.2
Hive 1.2.1
Sqoop 1.4.6
当我将Hive替换成2.0时，会包缺包的错误，此时，从Hive中拷贝一些包到$SQOOP_HOME/lib下即可。
```bash
cp $HIVE_HOME/lib/log4j-1.2-api-2.4.1.jar $SQOOP_HOME/lib/
cp $HIVE_HOME/lib/log4j-api-2.4.1.jar $SQOOP_HOME/lib/
cp $HIVE_HOME/lib/log4j-core-2.4.1.jar $SQOOP_HOME/lib/
cp $HIVE_HOME/lib/antlr-2.7.7.jar $SQOOP_HOME/lib/
cp $HIVE_HOME/lib/antlr4-runtime-4.5.jar $SQOOP_HOME/lib/
cp $HIVE_HOME/lib/antlr-runtime-3.4.jar $SQOOP_HOME/lib/
cp $HIVE_HOME/lib/calcite-avatica-1.5.0.jar $SQOOP_HOME/lib/
cp $HIVE_HOME/lib/calcite-core-1.5.0.jar $SQOOP_HOME/lib/
cp $HIVE_HOME/lib/calcite-linq4j-1.5.0.jar $SQOOP_HOME/lib/
cp $HIVE_HOME/lib/hive-cli-2.0.0.jar $SQOOP_HOME/lib/
```

## 附录：

此处创建的表bigdata用于Sqoop实战导入导出：
```bash
/*Create table */
drop table bigdata;
create TABLE bigdata(
class_id int,
class_name varchar(30),
class_month int,
teacher varchar(20),
last_mod_ts timestamp DEFAULT CURRENT_TIMESTAMP
/*PRIMARY KEY (`class_id`)*/
);

/*Insert data */
insert into bigdata(class_id,class_name,class_month,teacher) values(1,'bigdata intro.',8,'Mars');
insert into bigdata(class_id,class_name,class_month,teacher) values(2,'hadoop intro.',8,'Mars');
insert into bigdata(class_id,class_name,class_month,teacher) values(3,'hadoop components',8,'Mars');
insert into bigdata(class_id,class_name,class_month,teacher) values(4,'hadoop arch.',8,'Mars');
insert into bigdata(class_id,class_name,class_month,teacher) values(5,'hdfs',9,'Mars');
insert into bigdata(class_id,class_name,class_month,teacher) values(6,'yarn',9,'Mars');
insert into bigdata(class_id,class_name,class_month,teacher) values(7,'sqoop',9,'Mars');
insert into bigdata(class_id,class_name,class_month,teacher) values(8,'hive',9,'Mars');
insert into bigdata(class_id,class_name,class_month,teacher) values(9,'Hive example:log analysis',10,'Mars');
insert into bigdata(class_id,class_name,class_month,teacher) values(10,'hbase',10,'Mars');
insert into bigdata(class_id,class_name,class_month,teacher) values(11,'kylin',11,'Mars');
insert into bigdata(class_id,class_name,class_month,teacher) values(12,'spark intro.',11,'Mars');
insert into bigdata(class_id,class_name,class_month,teacher) values(13,'spark arch',11,'Mars');
insert into bigdata(class_id,class_name,class_month,teacher) values(14,'spark sql',12,'Mars');
insert into bigdata(class_id,class_name,class_month,teacher) values(15,'Spark SQL example:log analysis',12,'Mars');
insert into bigdata(class_id,class_name,class_month,teacher) values(16,'spark mllib',1701,'Mars');
insert into bigdata(class_id,class_name,class_month,teacher) values(17,'SparkR',1702,'Mars');
insert into bigdata(class_id,class_name,class_month,teacher) values(18,'Spark Project',1703,'Mars');
```

参考： 

 - https://ask.hellobi.com/blog/marsj/5093