---
title: "抛弃jQuery：Ajax"
categories: [ "JS" ]
tags: [ "jquery","ajax" ]
draft: false
slug: "abandon-jqueryajax"
date: "2015-10-02 12:30:00"
---

原文链接：http://blog.garstasio.com/you-dont-need-jquery/ajax/

## Ajax
大部分人开发者在学习jQuery的时候，看到 $.ajax() 函数都会觉得这真是太棒了！是的，它魔法般的把 ajax 请求变得极其容易，同时提供了足够多的控制项。在浏览器API层面这些请求都是由 XMLHttpRequest 对象完成的，$.ajax() 函数对它做了包装，这篇文章里快速浏览一下如何通过浏览器API提交ajax请求，包括跨域的ajax请求，试过之后可以发现这其实也不是很复杂。

> 译者注：在工程中使用原生代码进行Ajax操作是不明智的，作者也并非希望手工处理浏览器差异，只是希望能够理解jQuery背后的工作原理。

GETting
POSTing
URL Encoding
JSON
Uploading
CORS
JSONP
Libraries to Consider
Next in this Series

## GET 请求

我们从一个简单的请求开始，向服务器提交一个查询，通过用户的ID查询用户的名字，在URI的参数中包含了请求的用户ID。在请求结束之后弹出alert对话框提示结果。


<!--more-->


## jQuery

在jQuery中提交GET请求有两种方法，可以使用 $.get() 函数进行简单的请求，或者使用 $.ajax() 函数来获得更多的控制。在这里我们统一使用 $.ajax() 函数来统一所有的请求。

    $.ajax('myservice/username', {
      data: {
          id: 'some-unique-id'
      }
    })
    .then(
      function success(name) {
          alert('User's name is ' + name);
      },
     
      function fail(data, status) {
          alert('Request failed.  Returned status of ' + status);
      }
    );

 
*Native XMLHttpRequest Object:*

    var xhr = new XMLHttpRequest();
    xhr.open('GET', encodeURI('myservice/username?id=some-unique-id'));
    xhr.onload = function() {
        if (xhr.status === 200) {
            alert('User's name is ' + xhr.responseText);
        }
        else {
            alert('Request failed.  Returned status of ' + xhr.status);
        }
    };
    xhr.send();

上面这段JS代码在 [**IE 7及以上的浏览器**](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) 中都能正常的工作，即使是[**IE 6中也提供了有限的支持**](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/Using_XMLHttpRequest_in_IE6)。

## POST 请求

上一节通过GET请求我们已经能够通过用户ID获取用户的名字，在这里我们进一步修改他/她的名字，并为其添加“地址”字段。当然这个操作通过GET请求也是可以完成的，但仍然推荐使用POST请求。我们需要向服务器提交POST请求，请求中包含了用户的新名字以及地址信息，服务器收到请求后会加以处理并将更新后的名字返回，在JS里我们要验证返回的新名字与我们要修改的是否一致。

如果考虑到`HTTP 1.1`标准，这里我们更应该使用PATCH请求，PATCH并不是传统意义上通用的请求方式，在IE 8等较老的浏览器中存在某些问题，因此这里而我们只讨论POST请求。对于代码来说这两种请求的区别倒是不大，只有请求的方式字段不同。

*jQuery：*

    var newName = 'John Smith';
     
    $.ajax('myservice/username?' + $.param({id: 'some-unique-id'}), {
        method: 'POST',
        data: {
            name: newName
        }
    })
    .then(
        function success(name) {
            if (name !== newName) {
                alert('Something went wrong.  Name is now ' + name);
            }
        },
     
        function fail(data, status) {
            alert('Request failed.  Returned status of ' + status);
        }
    );

 
*Native XMLHttpRequest Object:*

    var newName = 'John Smith',
    
        xhr = new XMLHttpRequest(); 
        xhr.open('POST',
        encodeURI('myservice/username?id=some-unique-id'));
        xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
        xhr.onload = function() {
            if (xhr.status === 200 && xhr.responseText !== newName) {
                alert('Something went wrong.  Name is now ' + xhr.responseText);
            }
            else if (xhr.status !== 200) {
                alert('Request failed.  Returned status of ' + xhr.status);
            }
        };
        xhr.send(encodeURI('name=' + newName));
     

原生的API这样工作的也还算不错。
**译者注：**原生的方式写起来很复杂，而且需要手工处理 Content-Type 这样的HTTP头，手工判断返回的HTTP状态码。工程中强烈不推荐使用这样的代码，作者在这里强调原生API也能完成POST请求，只是为了说明jQuery背后的工作原理，并非推荐在工程中直接使用这样的代码。
## URL 编码

jQuery中提供了工具方法，对请求中需要传递的数据进行编码：

    $.param({
        key1: 'some value',
        'key 2': 'another value'
    });
     

在浏览器API中，提供了两个函数：[**encodeURI**](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/encodeURI) 和 [**encodeURIComponent**](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/encodeURIComponent) 用于进行URL转码，两者的[**区别**](http://www.cnblogs.com/qiantuwuliang/archive/2009/07/19/1526687.html)在于前者用于完整的URI的转码，因此URI中的合法字符（斜线等）不会被转码；后者用于URI的一部分（比如协议、主机名、路径或查询字符串）转码，因此所有可以转码的内容都会被转码。

如果我们想要“仿造”一个jQuery中的 $.param() 函数：


    function param(object) {
        var encodedString = '';
        for (var prop in object) {
            if (object.hasOwnProperty(prop)) {
                if (encodedString.length > 0) {
                    encodedString += '&';
                }
                encodedString += encodeURI(prop + '=' + object[prop]);
            }
        }
        return encodedString;
    }

 
### 发送/接收 JSON

RESTful接口大行其道的今天，JSON数据的发送/接收用的越来越多，这里我们假设需要向服务器发送待更新的数据，在服务器处理完更新之后，返回更新之后的整体数据。这里最适合的HTTP方法是 **PUT**：

*jQuery:*

    $.ajax('myservice/user/1234', {
        method: 'PUT',
        contentType: 'application/json',
        processData: false,
        data: JSON.stringify({
            name: 'John Smith',
            age: 34
        })
    })
    .then(
        function success(userInfo) {
            // userInfo will be a JavaScript object containing properties such as
            // name, age, address, etc
        }
    );
     

使用jQuery通过回调的方式提交Ajax请求有时候会把逻辑打散，事实上在不使用defer模式的情况下进行多重ajax请求是非常让人头晕的事情。jQuery默认的content-type是 application/x-www-form-urlencoded，如果需要使用其他的类型需要手工指定。

**译者注：** 作者这里对 $.ajax() 函数有误解，在 $.ajax() 函数中，data 的内容不需要手工通过 JSON.stringify() 进行序列化，而是可以通过指定 dataType=json 来实现对传入数据的序列化。

*Web API:*

    var xhr = new XMLHttpRequest();
    xhr.open('PUT', 'myservice/user/1234');
    xhr.setRequestHeader('Content-Type', 'application/json');
    xhr.onload = function() {
        if (xhr.status === 200) {
            var userInfo = JSON.parse(xhr.responseText);
        }
    };
    xhr.send(JSON.stringify({
        name: 'John Smith',
        age: 34
    }));

 
由于对JSON的支持问题，上面这段代码只有在[**IE 8及以上的浏览器**](http://caniuse.com/#search=JSON)中才能正常工作，如果要兼容比较旧的版本，只需要在页面加载json.js就好。

## 上传文件

在IE 9以及更早的浏览器中，在页面里上传文件的唯一方法是提交一个带有 `<input type="file">` 标签的表单，在这里没有`Web API`可以使用。所以我们只能在现代浏览器中讨论上传文件，这里会用到[File API](http://www.w3.org/TR/FileAPI/)。

有两种方法可以上传文件，第一种是使用 `<form enctype="multipart/form-data">`将文件作为HTTP请求的一部分上传。第二种是将文件作为请求的整体上传到服务器，两种方法都需要使用 File 或 Blob 对象。

假设上传文件的HTML标签是：

    <input type="file" id="test-input">

 
*jQuery:*

首先可以通过 `multipart/form-data` 的方式上传文件：

    var file = $('#test-input')[0].files[0],
        formData = new FormData();
     
    formData.append('file', file);
     
    $.ajax('myserver/uploads', {
        method: 'POST',
        contentType: false,
        processData: false,
        data: formData
    });

 
*第二种方式：*

    var file = $('#test-input')[0].files[0];
     
    $.ajax('myserver/uploads', {
        method: 'POST',
        contentType: file.type,
        processData: false,
        data: file
    });

 
两种方式中，都使用了 `processData: false` 来防止jQuery对文件内容进行编码。

## XMLHttpRequest

第一种方式，multipart/form-data：

    var formData = new FormData(),
    file = document.getElementById('test-input').files[0],
    xhr = new XMLHttpRequest();
     
    formData.append('file', file);
    xhr.open('POST', 'myserver/uploads');
    xhr.send(formData);

 
*第二种方式：*

    var file = document.getElementById('test-input').files[0],
    xhr = new XMLHttpRequest(); 
    xhr.open('POST', 'myserver/uploads');
    xhr.setRequestHeader('Content-Type', file.type);
    xhr.send(file);

 
## 跨域请求

译者注：jQuery及原生API的Ajax的跨域请求展开是一个很长的话题，原作者在这里介绍的并不详细，因此只是对原文稍加翻译，译者或许会在后面补一篇专门针对跨域Ajax的介绍。
跨域请求，特别是IE 8及更早浏览器中的跨域请求这个专题展开讨论会很复杂，我们这里并不关心所有的细节，只讨论最基本的同源策略和跨域请求。[Mozilla开发者网络](https://developer.mozilla.org/zh-CN/)里可以找到关于跨域请求的详细说明。

在现代浏览器中，跨域请求和非跨域请求在代码实现层面没有太大区别，只需要在服务器上做好相应设置，浏览器会完成剩下的判断和处理工作。跨域中还有一个需要注意的问题是，cookie 在默认情况不会被跨域请求携带，必须在 XMLHttpRequest 中指定 withCredentials 标记才会提交跨域cookie。

*jQuery:*

    $.ajax('http://someotherdomain.com', {
        method: 'POST',
        contentType: 'text/plain',
        data: 'sometext',
        beforeSend: function(xmlHttpRequest) {
            xmlHttpRequest.withCredentials = true;
        }
    });

 
*XMLHttpRequest:*

    var xhr = new XMLHttpRequest();
    xhr.open('POST', 'http://someotherdomain.com');
    xhr.withCredentials = true;
    xhr.setRequestHeader('Content-Type', 'text/plain');
    xhr.send('sometext');

## JSONP

这里介绍JSONP并不是推荐你使用它，他会导致一些潜在的安全问题，在现代浏览器中如果可能，还是应该直接使用跨域Ajax。

对于初学JSONP的同学，名字可能会造成一些困扰，事实上这里并没有JSON数据在网络上传输。一个很常见的误解是服务器一定返回包含JSON数据的HTTP包，然后客户端通过JSONP回调来使用。事实上服务器返回的是一段包含了数据结构的脚本代码，而非JSON。

JSONP的实现方式并不优雅，它建立在 <script> 标签不需要遵守同源策略的基础上，同时需要在浏览器端和服务器端都针对JSONP调用书写特殊的代码，服务器通过解析 <script> 的请求地址，生成包含特定数据的文本并返回给客户端。

*jQuery:*

    $.ajax('http://jsonp-aware-endpoint.com/user', {
        jsonp: 'callback',
        dataType: 'jsonp',
        data: {
            id: 123
        }
    }).then(function(response) {
        // handle requested data from server
    });

 
我们在讨论一种并不优雅的实现方法，jQuery 中把他包装成和惯常ajax请求类似的格式。事实上下面这段代码更加接接近JSONP的本质：

*Without jQuery:*

    window.myJsonpCallback = function(data) {
        // handle requested data from server
    };
     
    var scriptEl = document.createElement('script');
    scriptEl.setAttribute('src',
        'http://jsonp-aware-endpoint.com/user?callback=myJsonpCallback&id=123');
    document.body.appendChild(scriptEl);

 
## 可以替代jQuery的库

上面已经阐述过了，在没有辅助库的帮助下，也可以通过原生API完成Ajax的大部分任务，如果为了简化开发流程同时兼顾浏览器兼容性，有几个库可以考虑：

[**fetch**](https://github.com/github/fetch): 这是一个polyfill（抱歉这个没有很好地翻译，可以[**看这里**](http://www.cnblogs.com/ziyunfei/archive/2012/09/17/2688829.html)），能够帮助我们处理浏览器兼容性，在旧的浏览器中提供较好的ajax支持。

[**xdomain**](https://github.com/jpillora/xdomain): 跨浏览器实现的跨域请求库，通过 [**Web Messaging API**](http://www.w3.org/TR/webmessaging/) 来实现。

[**Lightweight-JSONP]**(https://github.com/IntoMethod/Lightweight-JSONP): 轻量级JSONP库，提供了对JSONP的支持。
