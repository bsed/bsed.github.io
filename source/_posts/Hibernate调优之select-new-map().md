---
title: "Hibernate调优之select new map()"
categories: [ "Java" ]
tags: [ "hibernate","select new map" ]
draft: false
slug: "java-hibernate-select-new-map"
date: "2014-11-08 14:56:00"
---

Hibernate调优不只是设置一下lazy，调整一下由谁来维护这个字段而已。

这次要说的是对查询语句进行优化——select new map()。


<!--more-->


select new map语句结果说明。

语句一：
```
String hql="select s.name from Student s";

List ls=session.createQuery(hql).list();

for(String obj[]:ls){

System.out.pringln(obj[0]);

}
```
结果list中，每条记录对应一个object数组，object[]中每个元素为hql语句中列的序号（从0开始）。

语句二：

    String hql="select new map(s.name) from Student s";
    
    List ls=session.createQuery(hql).list();
    
    for(Map m:ls){
    
    System.out.pringln(m.get("0"));
    
    }

结果list中，每条记录对应一个map，map中key为hql语句中的序号，从0开始，key为字符，非数字。

语句三：

    String hql="select new map(s.name as name)  from Student s";
    
    List ls=session.createQuery(hql).list();
    
    for(Map m:ls){
    
    System.out.pringln(m.get("name"));
    
    }

结果list中，每条记录对应一个map，map中key为hql语句中的别名。

#### 详解map ####

此处的map对应的是JDK中的HashMap。

个人理解是Hibernate在对此hql语句解析的时候，遇到map这个关键字，然后将后面的列作为值，别名作为键（若无别名，则用数字代替）存入到一个HashMap中。具体的代码没有看，谁有兴趣找到那段代码了发一下。

#### 详解select new map的效率 ####

我第一眼看到这个代码，第一感觉是这样降低了效率。因为涉及到对字段的分析。并且认为map不是jdk的map而是hibernate中mapping的那个map。

而mapping中的map只有一个构造函数new Map(PersistentClass owner)。所以我最刚开始认为是将字段封装成一个PersistentClass类，然后在new一个Map。

但是后来我发现并不是这个类，mapping中的类都是用来做映射文件用的，也就是说我的方向错了。

而用`select new map`这个语句到底是降低了效率还是提高了效率了呢？

简单说一下就是：你在写sql语句的时候，select * from效率高还是select 字段1，字段2 from 效率高？

答案很显然，前者效率一定不高于后者，因为后者可以只选择有用的数据进行传输。

现在再回来看select new map，不就是这个道理么？下面是我坐了几个例子进行了一下效率的对比（我用的是100条数据）。

单表查询

     //开始查询
       long startTime=System.currentTimeMillis();
       String hql="select new map(u.name as name) from User u";
       List<Map> list;
       list=session.createQuery(hql).list();
       //查询完毕
       double time=(System.currentTimeMillis()-startTime);
       NumberFormat numberFormat=new DecimalFormat("0.000");
       System.out.println(numberFormat.format(time/1000));
       //循环读取数据
       for(Iterator iter=list.iterator();iter.hasNext();){
        Map map=(Map) iter.next();
        System.out.println(map.get("name"));
       }
       //循环读取完毕
       time=(System.currentTimeMillis()-startTime);
       System.out.println(numberFormat.format(time/1000));
    hql="from User";
       List<Map> list;
       list=session.createQuery(hql).list();
       //查询完毕
       double time=(System.currentTimeMillis()-startTime);
       NumberFormat numberFormat=new DecimalFormat("0.000");
       System.out.println(numberFormat.format(time/1000));
       //循环读取数据
       for(Iterator iter=list.iterator();iter.hasNext();){
        User map=(User) iter.next();
        System.out.println(map.getName());
       }
       //循环读取完毕
       time=(System.currentTimeMillis()-startTime);
       System.out.println(numberFormat.format(time/1000));

开始和结束时间分别为：0.201，0.210

多表查询

    //开始查询
       long startTime=System.currentTimeMillis();
       //String hql="select new map(r.name as name,a.username as username) from Role r,Admin a where r.id=a.role.id";
       String hql="select new map(r.name as name,a.username as username) from Admin a join a.role r";
       List<Map> list;
       list=session.createQuery(hql).list();
       //查询结束
       double time=(System.currentTimeMillis()-startTime);
       NumberFormat numberFormat=new DecimalFormat("0.000");
       System.out.println(numberFormat.format(time/1000));
       //读取数据
       for(Iterator iter=list.iterator();iter.hasNext();){
        Map map=(Map) iter.next();
        System.out.println(map.get("name")+","+map.get("username"));
       }
       //读取完毕
       time=(System.currentTimeMillis()-startTime);
       System.out.println(numberFormat.format(time/1000));
    
    ~~开始和结束时间分别为：0.171，0.181~~
    
    //开始查询
       long startTime=System.currentTimeMillis();
       String hql="from Admin";
       List<Map> list;
       list=session.createQuery(hql).list();
       //查询结束
       double time=(System.currentTimeMillis()-startTime);
       NumberFormat numberFormat=new DecimalFormat("0.000");
       System.out.println(numberFormat.format(time/1000));
       //读取数据
       for(Iterator iter=list.iterator();iter.hasNext();){
        Admin map=(Admin) iter.next();
        System.out.println(map.getRole().getName()+","+map.getUsername());
       }
       //读取完毕
       time=(System.currentTimeMillis()-startTime);
       System.out.println(numberFormat.format(time/1000));

开始和结束时间分别为：0.193，0.201

我对这个的定义就是，这是一个视图，是一个运行时创建的视图，而不是在数据库中创建好的视图。

通过上面的对比，我们不难发现，select new map可以选择特定的列作为数据进行展示。我测试的表一共5个字段，如果一个表的字段很多（50个），但只会用到其中几个，那么通过这种方法无疑会节省传输量，从而达到提高效率。

写在最后：

如果不是自己做了这么一个简单的Demo，那么我对select new map 的误解深的真实没边没落的。所以，不论遇到什么，都不可以根据自己的主观臆断去判定一件事的好与坏，必须要有相应的数据做依靠。

在此，也希望大家用select语句一定不要用select *。涉及到无用的字段比较多时，若遇到了效率瓶颈，那么可以考虑用select new map进行优化，若无瓶颈，则不建议用此方法。

因为Hibernate作为一个优秀的ORM框架存在，使我们以面向对象的态势来对待数据持久化，而如此做之后，等于你将封装好的对象又拆开了。

与select new map相似的还有select new list、select new set。只研究了此一个，没有研究别的，但大致原理应该是差不多的。

END...