---
title: "ibatis selectKey用法注意事项"
categories: [ "Java" ]
tags: [ "java","ibatis" ]
draft: false
slug: "selectkey-ibatis-usage-notes"
date: "2016-06-02 15:19:00"
---

在我们使用插入数据库时，某张表中的ID是自增长的，需要返回ID，然后再插入相关的关系表中。但是有时候同样也会抛出问题，比如以下场景：

开始使用int做为ID自增长，返回相关的主键值，但是后来改动，改为String类型，使用UUID或12位日期随机数 赋值，但是在ibatis 时，依然保留如下的配置：
```xml
  <insert id="insertRecord" parameterClass="dmGroupDO" >
    INSERT INTO table(id,name,status,uid,members)
     VALUES (#id#, #name#, #status#, #uId#, #members#)
      <selectKey keyProperty="id" resultClass="java.lang.String" >
        SELECT LAST_INSERT_ID() AS id
    </selectKey>
  </insert>
```


<!--more-->


以下是我在`oracle` 项目的设置， 后台不用用java UUID 再生成
```xml
<!-- 保存推荐规则 -->
	<insert id="saveDsRecRule" parameterClass="java.util.Map">
		<selectKey resultClass="String" keyProperty="id" type="pre">
			select to_char(sysdate,'yyyyMMddhhmmss') as id from dual
		</selectKey>

			insert into DS_REC_RULES(ID,REC_NAME,RECENT_DAYS,REC_DESC,DOMAIN,
			ALGRITHMS,SIMILARITY,RESULT_NUMBER,RESULT_DESC,UPDATE_DATE,TYPE,FLAG,CREATOR,CREATE_DATE
			) values 
			(#id#,#recName#,#recentDays#,#recDesc#,#domain#,#algrithms#,#similarity#,
			#resultNumber#,#resultDesc#,#updateDate#,#type#,#flag#,#creator#,#createDate#
			)
	</insert>
```
或者用uuid 作为主键id
```xml
<!-- 收藏某个报表, 报表操作明细信息表 -->
	<insert id="saveUserReportFav" parameterClass="java.util.Map">
		<selectKey resultClass="String" keyProperty="id" type="pre">
			select sys_guid() as id from dual
		</selectKey>

			insert into  DS_DET_REP_OPER
			(ID,REPORT_ID,REPORT_NAME,USER_CODE,USER_NAME,OPER_TYPE,FLAG,VISIT_IP,OPERATE_TIME,REMARK)
			values
			(#id#,#rid#,#reportName#,#userCode#,#userName#,#operType#,#flag#,#visitIp#,SYSDATE,'')

	</insert>
```
java代码：
```
if(StringUtil.isNullOrEmpty(id)){
				String insertId =  (String) client.insert("dsma.saveDsRecRule", param);
				if(insertId !=null){
					context.setData(Constant.RESULT,Constant.RESULT_SUCCESS);//添加成功
				}
			}else {
				param.put("id", id);
				client.update("dsma.updateDsRecRule", param);
				context.setData(Constant.RESULT,Constant.RESULT_MODIFY);//修改成功
			}
```