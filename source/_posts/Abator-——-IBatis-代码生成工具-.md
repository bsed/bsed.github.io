---
title: "Abator —— IBatis 代码生成工具 "
categories: [ "Java" ]
tags: [ "java","abator" ]
draft: false
slug: "abator-ibatis-code-generation-tool"
date: "2010-09-03 12:54:00"
---

## Abator

对于IBatis应用最烦人的重复工作就是不停的写DAO，DTO，和xml文件里面的sqlmap，其实很多时候这些工作是重复而且无聊的。好在我们还有abator （http://ibatis.apache.org/abator.html ），一个IBatis自动生成工具，他可以帮助你生成大量的DAO，DTO和sqlmap。
如果你使用eclipse作为IDE那么事情就很简单了（相信没有几个人不在用）：
安装Eclipse插件
1. 要求eclipse3.1，jdk1.4以上
2. 在eclipse上安装abator插件
    eclipse菜单栏 --> help --> Software Updates --> Find And Install...
    在弹出的对话框中选择 “Search for new features to install” 然后点击 “next”
    在对话框中点击按钮 New Remort Site...  在弹出的对话框中的URL输入框中填写 http://ibatis.apache.org/tools/abator ，Name输入框就随便写些什么了。
    点击Finish，安装插件，然后重新启动eclipse

下面测试一下功能
1. 新建一个java工程
2. 在eclipse菜单栏中兴建一个 abator 配置文件
    File -->  Abator  for  iBatis Configration File


<!--more-->


好的，我们完成了创建配置文件，下面打abator配置文件，看看里面怎么去配置
打开后内容基本上是这样
xml 代码

    <?xmlversion="1.0"encoding="UTF-8"?> <!DOCTYPE abatorConfiguration PUBLIC "-//Apache Software Foundation//DTD Abator for iBATIS Configuration 1.0//EN"    <abatorContext><!-- TODO: Add Database Connection Information --> <jdbcConnectiondriverClass="???" <classPathEntrylocation="???"/> <javaModelGeneratortargetPackage="???"targetProject="???"/> <sqlMapGeneratortargetPackage="???"targetProject="???"/> <daoGenerator="IBATIS"targetPackage="???"targetProject="???"/> <tableschema="???"tableName="???"> <columnOverridecolumn="???"property="???"/> </abatorConfiguration>

你需要做的是替换一些???：
1. 填写driverClass（jdbc驱动，例如oracle的就是oracle.jdbc.driver.OracleDriver）
2. 填写connectionURL（连接字符串，例如oracle的就是jdbc:oracle:thin:@192.168.0.246:1521:test）
3. 填写classPathEntry的location（jdbc驱动jar包的位置，例如E:/project/ibatistest/WebContent/WEB-INF/lib/ojdbc14.jar）
4. 填写javaModelGenerator，生成的DTO（java model 类）
    targetPackage：目标包的位置，如 com.test.dto
    targetProject：目标工程名称，填写配置文件所在的eclipse工程名
5. 填写sqlMapGenerator ，生成的xml sqlmap的相关配置
    targetPackage：目标位置，如 com.test.sqlmap
    targetProject：目标工程名称，填写配置文件所在的eclipse工程名
6. 填写daoGenerator ，生成的DAO的相关配置
    type：生成的dao实现的类型，如果你使用spring的话写SPRING，否则写IBATIS
    targetPackage：目标位置，如 com.test.dao
    targetProject：目标工程名称，填写配置文件所在的eclipse工程名
7. 配置相关数据库的表
    schema：数据库schema，oracle就是填写数据库的用户名
    tableName：表名
xml 代码

    <columnOverridecolumn="???"property="???"/>

     可以先不用配置，删除就可以了

下面开始生成：
在配置文件上点解右键，选择 “Generate iBatis Artifacts”
OK，看看生成了什么吧！
声明：JavaEye文章版权属于作者，受法律保护。没有作者书面许可不得转载。
我做的几个项目，一直在用这个来生成dao和xml文件，工作量减轻不少。
有些项目还自己扩展了abator

我用的是命令行方式(没用过eclipse插件)：
java -cp lib/abator.jar org.apache.ibatis.abator.api.AbatorRunner -configfile abator.xml -overwrite

看看我的配置：

    <?xml version="1.0" encoding="UTF-8"?>   <!DOCTYPE abatorConfiguration     PUBLIC "-//Apache Software Foundation//DTD Abator for iBATIS Configuration 1.0//EN"     <abatorConfiguration>       <abatorContext id="mssql" generatorSet="Java5">           <jdbcConnection driverClass="net.sourceforge.jtds.jdbc.Driver"            connectionURL="jdbc:jtds:sqlserver://127.0.0.1:1433/db" userId="user" password="password">               <classPathEntry location="lib/jtds-1.2.jar"/>           </jdbcConnection>           <javaTypeResolver>               <property name="forceBigDecimals" value="false"/>           </javaTypeResolver>           <javaModelGenerator                targetPackage="persistence.model" targetProject="..\project\src\main\java\">               <property name="enableSubPackages" value="true"/>               <property name="trimStrings" value="true"/>           </javaModelGenerator>           <sqlMapGenerator                targetPackage="." targetProject="..\project\src\main\resources\">               <property name="enableSubPackages" value="true"/>           </sqlMapGenerator>           <daoGenerator type="SPRING"            targetPackage="persistence.dao" targetProject="..\project\src\main\java\">               <property name="enableSubPackages" value="true"/>           </daoGenerator>           <table schema="dbo" tableName="%">               <property name="useActualColumnNames" value="true"/>               <!-- 简写配置文件,省略发下面注解的写法,生成文件时会显示警告信息,忽略之 -->               <generatedKey column="ID" sqlStatement="SqlServer" identity="true"/>           </table>       </abatorContext>   </abatorConfiguration>  

我们对Abator进行了定制修改,他生成的Critia比较好,我最喜欢.
只可惜类结构不太好,它是把他放在一个Example类里面的,我觉得应该可以定制这个类名.
另外好像还有一个bug,当你在Example里面调用createCritia之后,如果你没有给critia设置任何条件.最后生成的sql语句where子句后就没有条件,数据库自然会报错. 
