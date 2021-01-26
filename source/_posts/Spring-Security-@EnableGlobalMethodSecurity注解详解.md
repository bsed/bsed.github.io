---
title: "Spring Security @EnableGlobalMethodSecurity注解详解"
categories: [ "Java" ]
tags: [ "spring","security","EnableGlobalMethodSecurity" ]
draft: false
slug: "explanation-of-spring-security-enablegrobalmethodsecurity-annotation"
date: "2020-07-23 15:50:16"
---

## Spring Security默认是禁用注解的，要想开启注解，需要在继承WebSecurityConfigurerAdapter的类上加`@EnableGlobalMethodSecurity`注解，来判断用户对某个控制层的方法是否具有访问权限
```java
@Configuration
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class OAuth2SecurityConfiguration extends WebSecurityConfigurerAdapter {
 ...........................
}
```
## 例如下面代码就表示如果用户具有admin角色，就能访问listAllUsers方法，但是如果方法前不加@preAuthorize注解，意味着所有用户都能访问listAllUsers方法


<!--more-->


```java
    @PreAuthorize("hasRole('admin')")
    @RequestMapping(value = "/user/", method = RequestMethod.GET)
    @ResponseBody
    public List<User> listAllUsers() {
        List<User> users = userService.findAll();
        if(users.isEmpty()){
            return null;
        }
        return users;
    }
```  
## @EnableGlobalMethodSecurity详解

### @EnableGlobalMethodSecurity(securedEnabled=true) 开启@Secured 注解过滤权限

### @EnableGlobalMethodSecurity(jsr250Enabled=true) 开启@RolesAllowed 注解过滤权限 

### @EnableGlobalMethodSecurity(prePostEnabled=true) 使用表达式时间方法级别的安全性 4个注解可用

- @PreAuthorize 在方法调用之前,基于表达式的计算结果来限制对方法的访问
- @PostAuthorize 允许方法调用,但是如果表达式计算结果为false,将抛出一个安全性异常
- @PostFilter 允许方法调用,但必须按照表达式来过滤方法的结果
- @PreFilter 允许方法调用,但必须在进入方法之前过滤输入值