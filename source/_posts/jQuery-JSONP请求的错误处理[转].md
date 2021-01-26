---
title: "jQuery JSONP请求的错误处理[转]"
categories: [ "JS" ]
tags: [ "jquery","jsonp" ]
draft: false
slug: "error-handling-of-jsonp-jquery-request"
date: "2015-08-27 11:50:00"
---

> jQuery的ajax方法封装的非常好，使用它你可以非常轻松的编写异步请求代码，但如果你是用它来做JSONP跨域请求，那你可要注意了，此处有坑！

以下是常见的jqeury jsonp请求的代码:

```
$.ajax({
  url: 'https://api.github.com/users/webfd/repos',
  type: 'GET',
  dataType: 'jsonp', // dataType为jsonp
  success: function(data) {
      $('.result').text(JSON.stringify(data));
  },
  error: function(jqXHR, textStatus) {
      $('.result').text('error');
      alert('JSONP error!');
  }
});
```


<!--more-->


以上方法看似安全可行，但如果你把url参数改成某个不存在的地址，你会惊奇的发现：虽然浏览器终端报出错误(404或其他网络错误)，但你的error回调却没有被执行!?

这是为什么呢？我们仔细看看[ajax](http://api.jquery.com/jquery.ajax/)方法的文档，在它error回调说明的最后有一句话：
`Note: This handler is not called for cross-domain script and cross-domain JSONP requests. This is an Ajax Event.`

这句话的意思是在调用跨域的脚本及JSONP请求时，这个错误回调将不会被执行，并强调指出这个error是一个 Ajax Event.

什么意思？难道说jsonp不是ajax?

是的，严格意义上来说jsonp不是ajax，ajax是通过XMLHttpRequest对象发送异步请求，而jsonp则是利用js标签天生具备的跨域能力来实现跨域资源访问。也就是说，虽然它的名字叫JSONP（JSON with Padding），但它底层是通过js标签实现的，它跟json以及普通异步请求关系都不大，jquery之所以把jsonp请求放到ajax方法里面，只是为了让api更方便调用而已。 （JSONP的详细描述可以查看[WIKI](http://zh.wikipedia.org/zh/JSONP)，或者google）

那怎么做才能使JSONP的error回调被执行呢？

有两个方法，方法一：添加timeout参数。
```
$.ajax({
  url: 'https://api.github.com/users/webfd/repos',
  type: 'GET',
  dataType: 'jsonp', // dataType为jsonp
  timeout: 5000, // 添加timeout参数        
  success: function(data) {
      $('.result').text(JSON.stringify(data));
  },
  error: function(jqXHR, textStatus) { // 此时textStatus为‘timeout’
      $('.result').text('error');
      alert('JSONP error!');
  }
});
```

添加timeout参数后，虽然JSONP请求本身的错误没有被捕获，但是最终会因为超时而执行error回调。

那还有没有方法使error回调在JSONP错误发生时就执行呢？

方法二出场：使用jquery jsonp插件 - [https://github.com/jaubourg/jquery-jsonp](https://github.com/jaubourg/jquery-jsonp)

以下为常见的jqeury jsonp插件调用代码:
```
$.jsonp({
  url: 'https://api.github.com/users/webfd/repos',
  callbackParameter: "callback",
  timeout: 5000,
  error: function(xOptions, textStatus) { // 错误发生时，立即执行
      $('.result').text('error');
      alert('JSONP error!');
  },
  success: function(data) {
      $('.result').text(JSON.stringify(data));
  }
});
```

使用jsonp插件，能够在错误发生时立即执行error回调，并且还附带如’数据过滤’等功能，更多详细介绍请查看它的主页。

以上。
yuanwe:[http://jarontai.github.io/blog/2014/08/28/jquery-jsonp-error-process/](http://jarontai.github.io/blog/2014/08/28/jquery-jsonp-error-process/)