---
title: js获取来源的URL代码 
date: 2014-10-12 11:09:00
updated: 2015-11-08 11:13:15
tags: 
- emacs
categories: 
- linux

---
## eferrer

正常取来源网页的URL只要用

`document.referrer`

## opener

如果来源页是Javascript跳转过来的，上边的方法就拿不到了！所以用

`opener.location.href`


<!--more-->


## 合并

    var ref = ''; 
    if (document.referrer.length > 0) { 
        ref = document.referrer; 
    }try { 
        if (ref.length == 0 && opener.location.href.length > 0) { 
            ref = opener.location.href; 
        } 
    } catch (e) {}

## 其它
### 获取域名后面的

    window.location.pathname

### 获取URL

    window.location.href

### 获取域名

`/[a-zA-Z0-9][-a-zA-Z0-9]{0,62}(\.[a-zA-Z0-9][-a-zA-Z0-9]{0,62})+(:[0-9]{1,4})?/` 域名加端口号

    var urlReg = /[a-zA-Z0-9][-a-zA-Z0-9]{0,62}(\.[a-zA-Z0-9][-a-zA-Z0-9]{0,62})+\.?/;  
    var url=urlReg.exec("http://www.baidu.com/aaaa");  
    console.log(url[0]);

# ## 获取URL参数

    location.search //=> “?hello=1&wcj=2”
    location.search.substr(1) //=> “hello=1&wcj=2”
    location.search.substr(1).split('&') //=> [“hello=1”, “wcj=2”]

### 获取URL返回JSON

    var url=location.search.substr(1).split('&'),
        i=url.length,
        _url,
        GET={};
        
    while(i--){
        _url = url[i].split('=');
        GET[_url.shift()]=decodeURIComponent(_url);
    }
    console.log(GET)  //=> Object {wcj: "2", hello: "1"}

### 获取URL参数

    /*
     * 获取特定字段的查询字符串
     * @param {String} name: 查询字符串的字段名
     */
    function getQuerySrting(name) {
        var search = location.search.substring(1).split('&');
        var value = false;
        for (var i = 0;i < search.length;i++) {
            var keyValue = search[i].split('=');
            if (keyValue[0] == name) {
                value = keyValue[1];
            }
        }
        return value;
    }


