---
title: "Spring中的@Transaction配置详解"
categories: [ "Java" ]
tags: [ "spring","transaction" ]
draft: false
slug: "detailed-transaction-configuration-in-spring"
date: "2013-08-17 13:26:00"
---

### 1、Spring默认Transactional事物管理机制
如果程序抛出的是运行期例外，则数据回滚 事物处理
如果是运行Exception例外，则数据不会滚。
可以通过配置修改该规则

    @Transactional(noRollbackFor=RuntimeException.class)方法事物说明
    @Transactional(RollbackFor=Exception.clas)
    @Transactional(readyOnly=true)
    @Transactional(timeout=100)默认30
    @Transactional(isolation)数据库的隔离级别
    {
    Read Uncommited:读取未提交的数据(会出现脏读 不可重复读 幻读)
    Read Commited:读已提交的数据(会出现不可重复读和幻读)
    Repeatable Read:可重复读(会出现幻读)
    Serializable:串行化
    }


<!--more-->


**脏读**:一个事务读取到另外一个事务未提交的更新的数据
不可重复读:在同一个事务中，多次读取同一个数据返回结果有所不同，就是后续的读取可以读到另外一个事务的已经提交的更新数据
可重复读:在同一个事务多次读取数据时，能够保证所读取的数据一样，也就是后读取的不能读到另外一个事务已经提交的数据
幻读: 一个事务读取到另外一个事务已经提交的更新的数据

针对查询方法

    @Transactional(propagation=Propagation.NOT_SUPPORTED)针对某个方法不开启事务
    @Transactional(propagation=Propagation.REQUIRED)spring默认的事务支持

### Propagation参数解析
1、REQUIRED:业务方法需要在一个事务中运行。如果方法运行中，已经处在一个事务中，那么加入到该事务，否则为自己创建一个新的事务。
2、NOT_SUPPORIED：声明方法不需要事务。如果方法没有关联到一个事务，容器不会为它开启事务。如果方法在一个事务中被调用，该事务会被挂起，在方法调用结束后，原先的事务便会恢复执行。
3、REQUIRES_NEW：属性表明不管是否存在事务，业务方法总会为自己发起一个新的事务。如果方法已经运行在一个事务中，则原有事务会被挂起， 新的事务会被创建，直到方法执行结束，新事务才算结束，原先的事务才会恢复执行。
4、MANDA

