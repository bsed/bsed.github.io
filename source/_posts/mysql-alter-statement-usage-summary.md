---
title: MySQL之alter语句用法总结
date: 2012-11-12 23:26:00
updated: 2017-02-18 23:28:03
tags: 
- ecmascript6
- destructuring
categories: 
- js

---
1：删除列
ALTER TABLE 【表名字】 DROP 【列名称】
2：增加列
ALTER TABLE 【表名字】 ADD 【列名称】 INT NOT NULL COMMENT '注释说明'
3：修改列的类型信息
ALTER TABLE 【表名字】 CHANGE 【列名称】【新列名称（这里可以用和原来列同名即可）】 BIGINT NOT NULL COMMENT '注释说明'
4：重命名列


<!--more-->


ALTER TABLE 【表名字】 CHANGE 【列名称】【新列名称】 BIGINT NOT NULL COMMENT '注释说明'
5：重命名表
ALTER TABLE 【表名字】 RENAME 【表新名字】
6：删除表中主键
Alter TABLE 【表名字】 drop primary key
7：添加主键
ALTER TABLE sj_resource_charges ADD CONSTRAINT PK_SJ_RESOURCE_CHARGES PRIMARY KEY (resid,resfromid)
8：添加索引
ALTER TABLE sj_resource_charges add index INDEX_NAME (name);
9: 添加唯一限制条件索引
ALTER TABLE sj_resource_charges add unique emp_name2(cardnumber);
10: 删除索引
alter table tablename drop index emp_name;
