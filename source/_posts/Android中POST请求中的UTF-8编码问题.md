---
title: "Android中POST请求中的UTF-8编码问题"
categories: [ "Android" ]
tags: [ "android","post" ]
draft: false
slug: "android-post-request-utf-8-set"
date: "2013-09-10 15:28:00"
---

今天遇到这样一个bug：客户端POST到服务器的一段数据导致服务器端发生未知异常。服务器端确认是编码转换错误。于是截取网络数据包进行分析，发现客户端POST的json数据中包含下面一段（hex形式）：

    ... 61 64 20 b7 20 52 69 63 ...

问题就出在这个`b7`上。查阅Unicode代码表后发现，`U+00b7`是MIDDLE DOT，它的UTF-8表现形式应该是`c2 b7`，但为何客户端发送的数据中它变成了`b7`？


<!--more-->


由于系统使用了[ormlite](http://ormlite.com/)、[gson](https://code.google.com/p/google-gson/)和[async-http](http://loopj.com/android-async-http/)几个库，于是逐一排查。最后发现原来是向服务器发送数据时没有指定文字编码，导致async-http（实际是[apache common http client](http://developer.android.com/reference/org/apache/http/client/HttpClient.html)）将数据以ISO-8559-1格式发送，U+00b7被编码成b7，然后服务器试图使用UTF-8解码时发生错误。

出错的代码片段如下：

    Gson gson = new Gson();
    String json = gson.toJson(data);
    StringEntity entity = new StringEntity(json);
    httpClient.post(context, url, entity, "application/json", new TextHttpResponseHandler() ... );

第三行`new StringEntity(json)`时没有指定编码导致错误。改正后如下：

    Gson gson = new Gson();
    String json = gson.toJson(data);
    StringEntity entity = new StringEntity(json, "utf-8");
    httpClient.post(context, url, entity, "application/json;charset=utf-8", new TextHttpResponseHandler() ... );