---
title: 兼顾效率，iBatis一些非见用法(10条)
date: 2010-05-05 13:40:00
updated: 2016-06-02 10:38:26
tags: 
- mysql
categories: 
- sql

---
[http://lavasoft.blog.51cto.com/62575/202886/](http://lavasoft.blog.51cto.com/62575/202886/)

iBatis一些非见用法，基本上解决所有棘手问题，下面总结如下：
## 1、动态SQL片段
通过SQL片段达到代码复用


<!--more-->


```
        <!-- 动态条件分页查询 --> 
        <sql id="sql_count"> 
                select count(*) 
        </sql> 
        <sql id="sql_select"> 
                select * 
        </sql> 
        <sql id="sql_where"> 
                from icp 
                <dynamic prepend="where"> 
                        <isNotEmpty prepend="and" property="name"> 
                                name like '%$name$%' 
                        </isNotEmpty> 
                        <isNotEmpty prepend="and" property="path"> 
                                path like '%path$%' 
                        </isNotEmpty> 
                        <isNotEmpty prepend="and" property="area_id"> 
                                area_id = #area_id# 
                        </isNotEmpty> 
                        <isNotEmpty prepend="and" property="hided"> 
                                hided = #hided# 
                        </isNotEmpty> 
                </dynamic> 
                <dynamic prepend=""> 
                        <isNotNull property="_start"> 
                                <isNotNull property="_size"> 
                                        limit #_start#, #_size# 
                                </isNotNull> 
                        </isNotNull> 
                </dynamic> 
        </sql> 
        <select id="findByParamsForCount" parameterClass="map" resultClass="int"> 
                <include refid="sql_count"/> 
                <include refid="sql_where"/> 
        </select> 
        <select id="findByParams" parameterClass="map" resultMap="icp.result_base"> 
                <include refid="sql_select"/> 
                <include refid="sql_where"/> 
        </select>
 ```
## 2、数字范围查询
所传参数名称是捏造所得，非数据库字段，比如_img_size_ge、_img_size_lt字段
``
                        <isNotEmpty prepend="and" property="_img_size_ge"> 
                                <![CDATA[ 
                                img_size >= #_img_size_ge# 
                        ]]> 
                        </isNotEmpty> 
                        <isNotEmpty prepend="and" property="_img_size_lt"> 
                                <![CDATA[ 
                                img_size < #_img_size_lt# 
                        ]]> 
                        </isNotEmpty> 
 
多次使用一个参数也是允许的
                        <isNotEmpty prepend="and" property="_now"> 
                                <![CDATA[ 
                                            execplantime >= #_now# 
                                     ]]> 
                        </isNotEmpty> 
                        <isNotEmpty prepend="and" property="_now"> 
                                <![CDATA[ 
                                            closeplantime <= #_now# 
                                     ]]> 
                        </isNotEmpty>
 ``
## 3、时间范围查询
```
                        <isNotEmpty prepend="" property="_starttime"> 
                                <isNotEmpty prepend="and" property="_endtime"> 
                                        <![CDATA[ 
                                        createtime >= #_starttime# 
                                        and createtime < #_endtime# 
                                 ]]> 
                                </isNotEmpty> 
                        </isNotEmpty> 
 ```
## 4、in查询
```
                        <isNotEmpty prepend="and" property="_in_state"> 
                                state in ('$_in_state$') 
                        </isNotEmpty>
 ```
## 5、like查询
```
                        <isNotEmpty prepend="and" property="chnameone"> 
                                (chnameone like '%$chnameone$%' or spellinitial like '%$chnameone$%') 
                        </isNotEmpty> 
                        <isNotEmpty prepend="and" property="chnametwo"> 
                                chnametwo like '%$chnametwo$%' 
                        </isNotEmpty> 
 ```
## 6、or条件
```
                        <isEqual prepend="and" property="_exeable" compareValue="N"> 
                                <![CDATA[ 
                                (t.finished='11'    or t.failure=3) 
                        ]]> 
                        </isEqual>
 
                        <isEqual prepend="and" property="_exeable" compareValue="Y"> 
                                <![CDATA[ 
                                t.finished in ('10','19') and t.failure<3 
                        ]]> 
                        </isEqual>
 ```
## 7、where子查询
```
                        <isNotEmpty prepend="" property="exprogramcode"> 
                                <isNotEmpty prepend="" property="isRational"> 
                                        <isEqual prepend="and" property="isRational" compareValue="N"> 
                                                code not in 
                                                (select t.contentcode 
                                                from cms_ccm_programcontent t 
                                                where t.contenttype='MZNRLX_MA' 
                                                and t.programcode = #exprogramcode#) 
                                        </isEqual> 
                                </isNotEmpty> 
                        </isNotEmpty>
 
        <select id="findByProgramcode" parameterClass="string" resultMap="cms_ccm_material.result">
                select * 
                from cms_ccm_material 
                where code in 
                (select t.contentcode 
                from cms_ccm_programcontent t 
                where t.contenttype = 'MZNRLX_MA' 
                and programcode = #value#) 
                order by updatetime desc 
        </select>
 ```
## 9、函数的使用
```
        <!-- 添加 --> 
        <insert id="insert" parameterClass="RuleMaster"> 
                insert into rulemaster( 
                name, 
                createtime, 
                updatetime, 
                remark 
                ) values ( 
                #name#, 
                now(), 
                now(), 
                #remark# 
                ) 
                <selectKey keyProperty="id" resultClass="long"> 
                        select LAST_INSERT_ID() 
                </selectKey> 
        </insert> 
        <!-- 更新 --> 
        <update id="update" parameterClass="RuleMaster"> 
                update rulemaster set 
                name = #name#, 
                updatetime = now(), 
                remark = #remark# 
                where id = #id# 
        </update>
 ```
## 10、map结果集
```
        <!-- 动态条件分页查询 --> 
        <sql id="sql_count"> 
                select count(a.*) 
        </sql> 
        <sql id="sql_select"> 
                select a.id                vid, 
                a.img             imgurl, 
                a.img_s         imgfile, 
                b.vfilename vfilename, 
    b.name            name, 
                c.id                sid, 
                c.url             url, 
                c.filename    filename, 
                c.status        status 
        </sql> 
        <sql id="sql_where"> 
                From secfiles c, juji b, videoinfo a 
                where 
                a.id = b. videoid 
                and b.id = c.segmentid 
                and c.status = 0 
                order by a.id asc,b.id asc,c.sortnum asc 
                <dynamic prepend=""> 
                        <isNotNull property="_start"> 
                                <isNotNull property="_size"> 
                                        limit #_start#, #_size# 
                                </isNotNull> 
                        </isNotNull> 
                </dynamic> 
        </sql> 
        <!-- 返回没有下载的记录总数 --> 
        <select id="getUndownFilesForCount" parameterClass="map" resultClass="int"> 
                <include refid="sql_count"/> 
                <include refid="sql_where"/> 
        </select> 
        <!-- 返回没有下载的记录 --> 
        <select id="getUndownFiles" parameterClass="map" resultClass="java.util.HashMap"> 
                <include refid="sql_select"/> 
                <include refid="sql_where"/> 
        </select>
 ```
注意：在使用Map作为结果集返回类型时候，必须这么设置结果集类型resultClass="java.util.HashMap"，这时候，需要根据字段的名称来取值，值类型为Object，key类型为String，这点要注意了：
 
定义的DAO方法实现如下：
```
        public List<Map<String,Object>> findUndownFiles(Map map) { 
                return getSqlMapClientTemplate().queryForList("secfiles.getUndownFiles", map); 
        }
``` 
通过DAO读取并操作Map结果集数据：
```
        public void test_findUndownFiles() { 
                List<Map<String, Object>> co = ser.findUndownFiles(new HashMap()); 
                StringBuilder s = new StringBuilder(); 
                for (Map<String, Object> map : co) { 
                        System.out.println("---------------------------"); 
                        for (Map.Entry<String, Object> entry : map.entrySet()) { 
                                System.out.print(entry.getKey()+"\t"); 
                                System.out.println(entry.getValue()); 
                        } 
                } 
        }
 ```
打印结果：
```
--------------------------- 
sid  1 
vfilename  200905252009235799 
url  http://d18.v.iask.com/f/1/f47817a394730dc682e660b943e84cc41006606.flv 
status  0 
filename  200905252009235799-00.flv 
imgfile  200905252009234399.jpg 
vid  1 
imgurl  http://p4.v.iask.com/95/595/1757503_1.jpg 
--------------------------- 
sid  2130 
vfilename  2009062615063867492 
url  http://lz.dhot.v.iask.com/f/1/0ee2ae8b973988f6a93c071c8045ca5217266409.mp4 
status  0 
filename  2009062615063867492-00.mp4 
imgfile  2009062615063825434.jpg 
vid  93 
imgurl  http://cache.mars.sina.com.cn/nd/movievideo//thumb/2/1502_120160.jpg 
--------------------------- 
sid  2131 
vfilename  2009062615064184076 
url  http://lz5.dhot.v.iask.com/f/1/36d3dadacb8d6bda434a58e7418ad3cc19037464.flv 
status  0 
filename  2009062615064184076-00.flv 
imgfile  2009062615064136733.jpg 
vid  94 
imgurl  http://cache.mars.sina.com.cn/nd/movievideo//thumb/6/2106_120160.jpg
```