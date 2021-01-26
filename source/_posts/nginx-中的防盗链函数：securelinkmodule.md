---
title: "nginx 中的防盗链函数：securelinkmodule"
categories: [ "Linux" ]
tags: [ "nginx","securelinkmodule" ]
draft: false
slug: "security-chain-function-securelinkmodule-in-nginx"
date: "2014-06-16 13:26:00"
---

nginx可以利用`securelinkmodule`模块来实现类似防盗链等需要授权访问还有通过时间戳控制url有效期。本文仅适用于下载服务器，不适用于图片防盗链。

1、编译时加上–with-http_secure_link_module选项即可

 - nginx版本需要0.7.18以上
 - 0.8.50+版本（更优的处理MD5哈希与增加过期头）
 - 在Nginx-0.8.50，这个模块增加了”secure_link_md5“指令和”secure_link_expires“变量，”secure_link_secret“指令已经不推荐使用


<!--more-->


2、nginx的配置
securelink是获取url的st和e参数(PS：`The md5_hash must be encoded using Base64 for URLs. expiration_time is the Unix epoch.`)
`securelinkmd5`计算segredo$uri$arg_e的md5值，segredo是密钥，$uri是当前url的uri值，$arg_e参数e，后面可以添加更多的验证规则

    location / {
        ## This must match the URI part related to the MD5 hash and expiration time.
        secure_link $arg_st,$arg_e;
     
        ## The MD5 hash is built from our secret token, the URI($path in PHP) and our expiration time.
        secure_link_md5 segredo$uri$arg_e;
     
        ## If the hash is incorrect then $secure_link is a null string.
        if ($secure_link = "") {
            return 403;
        }
     
        ## The current local time is greater than the specified expiration time.
        if ($secure_link = "0") {
            return 403;
        }
     
        ## If everything is ok $secure_link is 1.
    }

3、给url加上token和时间戳，secret是md5的密钥，expire是时间戳
示例：密钥为segredo,uri为`/tmp/nginx-1.4.7.tar.gz`，时间戳有效期3小时

    date -d "+3 hours" +%s
    echo -n 'segredo/tmp/nginx-1.4.7.tar.gz1397996438' | openssl md5 -binary | openssl base64 | tr +/ -_ | tr -d =
    
    #生成的url中有md5的值还有expire的参数结果
    
    http://127.0.0.1/tmp/nginx-1.4.7.tar.gz?st=czuQp-59WbiGi7zLU9PE1w&e=1397996438

4、secure link 防盗链原理

 - 用户访问http://127.0.0.1/tmp/nginx-1.4.7.tar.gz?st=czuQp-59WbiGi7zLU9PE1w&e=1397996438
 - nginx下载服务器接收到了过期时间，也使用过期时间、配置里密钥、文件uri生成加密串

将用户传进来的加密串与自己生成的加密串进行对比，加密串一致和时间未过期允许下载，否则403
整个过程实际上很简单，类似于用户密码验证. 尤为注意的一点是大家一定不要泄露了自己的密钥，否则别人就可以盗链了，除了泄露之外最好能经常更新密钥.
 
5、注意事项
密钥防止泄露、以及经常更新密钥
 
模块文档：
[http://wiki.nginx.org/HttpSecureLinkModule](http://wiki.nginx.org/HttpSecureLinkModule)
[http://nginx.org/en/docs/http/ngx_http_secure_link_module.html](http://nginx.org/en/docs/http/ngx_http_secure_link_module.html)