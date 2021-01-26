---
title: "Http相应报文-HttpResponse"
categories: [ "Java" ]
tags: [ "java","http","httpresponse" ]
draft: false
slug: "http-corresponding-message-httpresponse"
date: "2010-08-22 11:29:00"
---

```
package com.scl.base;

import org.apache.http.HttpResponse;
import org.apache.http.HttpStatus;
import org.apache.http.HttpVersion;
import org.apache.http.message.BasicHttpResponse;

public class HttpClientDemo03 {
	public static void main(String[] args) {
		/**
		 * Http响应是由服务器在收到和解析请求报文之后，返回给客户端的报文，
		 * 报文的第一行包含了协议的版本，数字状码和相关的文本段
		 * 
		 */
		//创建一个响应，响应一般是有服务器创建的，返回给客户端的。客户端会根据http协议的报文为客户端创建一个响应，一般无需储蓄员自己创建，直接可以获得
		HttpResponse response = new BasicHttpResponse(HttpVersion.HTTP_1_1, HttpStatus.SC_OK, "OK");
	


<!--more-->


	//
		System.out.println("http版本号：" + response.getProtocolVersion());
		//可以获得状态行，和状态行的各个元素的信息，和请求的状态行类似
		System.out.println("状态行信息：" + response.getStatusLine().toString());
		System.out.println("状态码：" + response.getStatusLine().getStatusCode());
		System.out.println("状态码的解释：" + response.getStatusLine().getReasonPhrase());
		//还有一些其他的方法，可以获得请求的实体与内容见下图
		
		
	}
}

```

图片丢失:>
这是第二篇，http相应报文的封装类的用法，和一些重要方法的解释
