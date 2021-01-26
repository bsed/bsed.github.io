---
title: "Spring Framework 的 @EnableWebSecurity的作用"
categories: [ "Java" ]
tags: [ "java","spring","EnableWebSecurity" ]
draft: false
slug: "spring-fr-x-ameworks-enable-web-security"
date: "2020-07-23 15:09:00"
---

## @EnableWebSecurity的作用

EnableWebSecurity注解是个组合注解,而且又使用了`@EnableGlobalAuthentication`注解:


<!--more-->


```java
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE})
@Documented
@Import({WebSecurityConfiguration.class//1, SpringWebMvcImportSelector.class, OAuth2ImportSelector.class})
@EnableGlobalAuthentication //2
@Configuration
public @interface EnableWebSecurity {
    boolean debug() default false;
}
```
## 激活了`WebSecurityConfiguration`配置类,在这个配置类中, 注入了一个非常重要的

bean(**springSecurityFilterChain**)，这是Spring Secuity的核心过滤器, 这是请求的认证入口。

2: 使用了`EnableGlobalAuthentication` 注解, 注解源码为:
```java
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE})
@Documented
@Import({AuthenticationConfiguration.class})
@Configuration
public @interface EnableGlobalAuthentication {
```

在这个注解中,激活了AuthenticationConfiguration配置类, 这个类是来配置认证相关的核心类, 这个类的主要作用是,向spring容器中注入AuthenticationManagerBuilder, AuthenticationManagerBuilder其实是使用了建造者模式, 他能建造AuthenticationManager, 这个类前面提过,是身份认证的入口。

EnableWebSecurity注解有两个作用,

1: 加载了WebSecurityConfiguration配置类, 配置安全认证策略。
2: 加载了AuthenticationConfiguration, 配置了认证信息。
