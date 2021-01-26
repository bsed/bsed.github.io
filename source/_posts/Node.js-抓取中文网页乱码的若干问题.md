---
title: "Node.js 抓取中文网页乱码的若干问题"
categories: [ "JS" ]
tags: [ "Node.js" ]
draft: false
slug: "some-problems-of-nodejs-capture-chinese-webpage-garbled-1"
date: "2015-05-11 20:28:00"
---

使用 `iconv-lite` 解决 request 乱码问题
Node.js 抓取非 utf-8 的中文网页时会出现乱码问题，比如网易的首页编码是 gb2312，抓取时会出现乱码

    var request = require('request')  
    var url = 'http://www.163.com'
    
    request(url, function (err, res, body) {  
        console.log(body)
    })

可以使用 iconv-lite 来解决

## 安装


<!--more-->


`npm install iconv-lite`
同时我们顺带把 user-agent 修改一下，以防网站屏蔽：

    var originRequest = require('request')  
    var iconv = require('iconv-lite')  
    var headers = {  
      'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/39.0.2171.65 Safari/537.36'
    }
    
    function request (url, callback) {  
      var options = {
        url: url,
        encoding: null,
        headers: headers
      }
      originRequest(options, callback)
    }
    
    request(url, function (err, res, body) {  
        var html = iconv.decode(body, 'gb2312')
        console.log(html)
    })

乱码问题解决

使用 cheerio 解析 HTML
cheerio 可以简单粗暴的理解为服务器端 jQuery 选择器，有了它，比正则要更加直观许多

安装

    npm install cheerio  
    request(url, function (err, res, body) {  
        var html = iconv.decode(body, 'gb2312')
        var $ = cheerio.load(html)
        console.log($('h1').text())
        console.log($('h1').html())
    })

输出如下

网易
&#x7F51;&#x6613;
那么问题来了，$('h1').html() 输出的代码是经过 Unicode 编码的，网易变成了&#x7F51;&#x6613;，给我们的字符处理带来了一些麻烦

解决 cheerio .html() 「乱码」问题
查阅文档可知，可以关闭这个转换实体编码的功能

`var $ = cheerio.load(html) ` 
改成

    var $ = cheerio.load(html, {decodeEntities: false})  

即可，完整代码如下：

    var originRequest = require('request')  
    var cheerio = require('cheerio')  
    var iconv = require('iconv-lite')  
    var headers = {  
      'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/39.0.2171.65 Safari/537.36'
    }
    
    function request (url, callback) {  
      var options = {
        url: url,
        encoding: null,
        headers: headers
      }
      originRequest(options, callback)
    }
    
    var url = 'http://www.163.com'
    
    request(url, function (err, res, body) {  
        var html = iconv.decode(body, 'gb2312')
        var $ = cheerio.load(html, {decodeEntities: false})
        console.log($('h1').text())
        console.log($('h1').html())
    })