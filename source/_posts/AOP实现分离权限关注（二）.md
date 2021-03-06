---
title: "AOP实现分离权限关注（二）"
categories: [ "Java" ]
tags: [ "java","aop" ]
draft: false
slug: "aop-to-achieve-separation-authority-concerns"
date: "2015-10-07 11:25:00"
---

我们知道，在基本的RBAC模型中有以下基本（接口）对象：Domain, Group, User, Role, Privilege, Operation, Resource以及对外的SecurityManager。Privilege通过Operate Resource而产生，Role则对应若干个Privilege。不同的Domain, Group, User拥有不同数量的Role，其中User的Role可以继承自Group，也可以继承自Domain；Group的权限可以继承自Domain。对外表现时，可以简单地通过SecurityMananger.checkPermission(User,  Privilege)的返回值true/false进行判断。上面每个对象都是接口，SecurityManager可以通过IoC反向注入，从而使得这种权限模型的使用比较灵活。

在实际情况中，这种权限系统中Resource的定义方法与粒度最难以控制。不同的业务系统中可能有不同的定义方式。而且这种方式的交流非常不方便。以一个普通的“科研项目管理”项目为例：系统中需要进行权限控制的东西可能有：用户信息（增删改查），项目信息（增删改查），日志信息（查看，统计）等等。这种权限的控制如果按照垂直编程的方式来写，需要进行以下工作：

研究：需要控制的资源到底是什么？用户信息？项目信息？这些东西如何表现？这个问题在这种模型中很难考虑清楚。最终的结果往往是，使用没有Resource接口的权限模型，形成一份新的文档：权限代码以及对应说明表。每个编程人员在编写相应的业务逻辑时，都需要参考这个表，并将程序中加上一序列的if --- else来判断权限。这种做法无疑是危险的。因为一旦权限代码发生变动， 这个业务方法马上得重写。稍微考虑不难知道，这种方法根本不可取：连基于URI的解决方案都不如。

可以看到，像用户信息、项目信息等等，都是业务对象，这些业务对象对于人是可理解的，但将这些业务对象作为RBAC中的资源是不合理的。因此我提出一种新的资源概念：将具体业务类、业务方法作为RBAC模型中的资源。这样才能够真正脱离权限与业务逻辑。具体做法是：将所有的业务方法名称以及对应的角色名称存入到XML文件或者数据库中，形成一个Resource库：


<!--more-->


    Method                   Role
    ------------------------------------------
    UserManager.*            UserManager
    UserManager.update       UserManager
    UserManager.update       User
    LogInfo.view             *
    LogInfo.statistics       LogManager
    ...                      ...
    ------------------------------------------

对于权限的判断，可以在RBAC的具体实现中进行。这里假定有一个SecurityManager.checkPermission(User, Privilege)(这里的Privilege实际上就是能否执行特定业务方法)的方法。根据这个方法，制作一个Aspect:

    public class PermissionCheckAdvice implements MethodBeforeAdvice {
        public void before(Method arg0, Object[] arg1, Object arg2)  throws Throwable {
              if (!SecurityManager.checkPermission(User, Privilege)) {
                    throws new PermissionDeniedException(User, Privilege);
              }
        }
    }

然后使用AOP将这个Advice应用到所有的业务方法（类或者方法可能在具有普遍意义的一个package中，也可能具有一定正则意义），并截获所有的PermissionDeniedException：

    public class PermissionThrowsAdvice implements ThrowsAdvice {
        public void afterThrowing(Method method, Object[] args, Object target,
                Throwable subclass) {
            //对Permission Denied Exception进行自己的处理。
        }
    
    }

对应的Spring Beans配置请参考上一片Blog。

`AOP`分离权限关注的文字到此截至，有些比较重要但是来不及完成的如SecurityManager如何引入，上下文环境如何取得等等，可能需要各位自行完成了。这里提出的是一种将Method作为资源的概念，希望对做权限系统的同仁有所帮助。