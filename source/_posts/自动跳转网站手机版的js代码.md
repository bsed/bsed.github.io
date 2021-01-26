---
title: "自动跳转网站手机版的js代码"
categories: [ "JS" ]
tags: [ "javascript","wap","mobile" ]
draft: false
slug: "automatic-jump-site-mobile-version-of-the-js-code"
date: "2015-08-01 15:49:00"
---

以下为代码，可放置在网站foot底部文件，或者haead顶部文件，建议将代码放在网站顶部，这样可以实现手机访问立即跳转！
```
<script src="http://siteapp.baidu.com/static/webappservice/uaredirect.js" type="text/javascript"></script>
<script type="text/javascript">uaredirect("http://m.yigrherb.com");</script>
```

将代码网址部分改成你网站的手机网址就Ok,当然可以是任意网址！
这应该就是传说中的WAP自动跳转吧！


<!--more-->


以下代码更全面些，经测试，可以兼容windows phone

     var browser_class = navigator.userAgent;
     var browser_class_name1 = browser_class.match("Mobile");
     var browser_class_name2 = browser_class.match("mobile");
     var location_url = window.location.href;
     if (browser_class_name1 != null || browser_class_name2 != null){
         if (location_url.match("wap") == null){
         window.location.href="http://m.yigrherb.com";
         }
     } else
     {
        if (location_url.match("3g") != null || location_url.match("wap") != null){
        window.location.href="http://m.yiherb.com";
        }
     }
     