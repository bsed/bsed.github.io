---
title: 使用AOP来分离权限关注（一）
date: 2015-10-06 11:09:00
updated: 2015-10-07 11:26:30
tags: 
- php
- namespace
categories: 
- php

---
对于权限管理的做法，在WEB实现上，无非有以下几种：

1 利用Filter，对所有进入的URI进行解析，并取得当时Session中的User信息，然后通过RBAC的机制，将此链接需要的权限与用户拥有的权限进行比较，然后进行相应的处理。这种做法有很多好处：简单，容易实现，并且对系统侵入性也不强。这里URL就是RBAC中的资源了。这样做的缺点是所有对数据的操作必须通过URL来体现，这一点在现代的程序中不太好实现。如果采用Struts, XWork或者Tapestry，采用同一个URL（浏览器看来）进行处理多项任务已不是什么稀奇的事。

2 利用一个BaseServlet(Servlet+Jsp经典模式)或者BaseAction(Struts模式)或者BasePage(Tapestry模式)或者BaseController(SpringMVC模式)，对所有的请求先进行过滤进行权限操作，然后再处理。稍微看一下就知道这种模式跟Filter并无本质不同。优缺点同上。

那么，如果要实现更为细致的权限操作，精确到某个方法的权限，该如何做呢？典型的做法如下：

    public someFunciton() {
      //权限判断
      User user = context.getUser();
      if (user.canExecuteThisFunction()) {
          // do the business method
          // ...
      } else {
         throw new PermissionDeniedException();
      }
    }


<!--more-->


这种做法能够将权限的粒度控制到具体的业务方法，因此它的控制能力应该是强大的。可以看到，权限判断部分对于每个方法几乎是独立的，同时也是无趣，易出错的。有了AOP，这一部分就可以忽略了，新的业务方法可以这样写：

    public someFunciton() {
          // do the business method
          // ...
    }

没有了额外的权限操作，这个业务方法看起来那么清晰自然。

将对权限的操作作为一个Advice，并将Advisor关注到所有的业务方法（可能有某一个特定package），然后，剩下的事情就由你的RBAC以及AOP来完成了。通过这样的分离，纵向的一个业务方法被分割为一个更为自然的业务方法和一个关注点。这个关注点写法可能如下：

    public class PermissionCheckAdvice implements MethodBeforeAdvice {
        public void before(Method arg0, Object[] arg1, Object arg2)  throws Throwable {
               //权限判断
              if (!this.getContext().getUser().canExcute(this, arg0)) {
                    throws new PermissionDeniedException();
              }
        }
    }

可能有个问题：如何取得context或者当时上下文环境的User呢？答案是使用IoC（或称Dependency Injection），将上下文环境或者User作为参数反向传入到逻辑方法中。当然，在传入之前，这些变量是需要初始化的。这个初始化工作可以在SuperServlet中进行，并且以Session单例的形式保存在应用程序中。下面是Spring配置文件的例子：

    <beans>
       <!-- Bean configuration -->
       <bean id="businesslogicbean"
       class="org.springframework.aop.framework.ProxyFactoryBean">
          <property name="proxyInterfaces">
             <value>IBusinessLogic</value>
          </property>
          <property name="target">
             <ref local="beanTarget"/>
          </property>
          <property name="interceptorNames">
             <list>
                <value>thePermissionCheckBeforeAdvisor</value>
             </list>
             </property>
       </bean>
    
       <!-- Bean Classes -->
       <bean id="beanTarget" class="BusinessLogic">
           <property name="user"><<YOUR USER OBJECT>></property>
       </bean>
    
       <!-- Advisor pointcut definition for before advice -->
       <bean id="thePermissionCheckBeforeAdvisor"
          class="org.springframework.aop.support.RegexpMethodPointcutAdvisor">
          <property name="advice">
             <ref local="thePermissionCheckBeforeAdvice"/>
          </property>
          <property name="pattern">
             <value>.*</value>
          </property>
       </bean>
       
       <!-- Advice classes -->
       <bean id="thePermissionCheckBeforeAdvice" class="PermissionCheckBeforeAdvice"/>
     
    </beans>

-----------------------------------------------------------

**注：**API名称还需要深刻考虑，这里仅用作解释使用。Spring的配置文件也仅作参考，可能有错误，实际中还需需要更多考虑。
