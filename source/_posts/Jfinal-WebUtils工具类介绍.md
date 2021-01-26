---
title: "Jfinal WebUtils工具类介绍"
categories: [ "Java" ]
tags: [ "java","webutils","jfinal" ]
draft: false
slug: "webutils-jfinal-tool-class"
date: "2015-05-09 21:21:00"
---

## 获取ctx_path，URL前面加上ctx_path
```
WebUtils.basePath();

WebUtils.concatBasePath(userURL);
```

## 密码加密，调用Jfinal的MD5
```
WebUtils.pwdEncode(password);
```

## 用户状态相关
```
//拿取登陆的Web/Wap用户信息
WebUtils.currentUser(Controller c);
WebUtils.currentUser(HttpServletRequest request, HttpServletResponse response);
//Web/Wap用户登陆
WebUtils.loginUser(Controller c, UserModel user, boolean... remember)
//Web/Wap用户退出
WebUtils.logoutUser(Controller c);

//拿取登陆的Admin用户信息
WebUtils.currentAdmin(Controller c);
//Admin用户登陆
WebUtils.loginAdmin(Controller c, UserModel user, boolean... remember);
//Admin用户退出
WebUtils.logoutAdmin(Controller c);

//拿取登陆的App用户信息
WebUtils.currentApp(Controller c);
//App用户登陆
WebUtils.loginApp(Controller c, UserModel user, boolean... remember);
//App用户退出
WebUtils.logoutApp(Controller c);
```


<!--more-->


## Cookie相关
```
//读取cookie
WebUtils.getCookie(HttpServletRequest request, String key);

//清除某个指定的cookie
WebUtils.removeCookie(HttpServletResponse response, String key);

//设置cookie,指定为httpOnly保证安全性,特用于用户状态
WebUtils.setCookie(HttpServletResponse response, String name, String value, int maxAgeInSeconds);
```

## 请求头信息
```
// 获取浏览器信息
WebUtils.getUserAgent(HttpServletRequest request);

// 获取用户ip
WebUtils.getIP(Controller c);
WebUtils.getIP(HttpServletRequest request);

// 判断用户的手机是否智能手机
WebUtils.isTouch(HttpServletRequest request);
WebUtils.isTouch(String userAgent);
```