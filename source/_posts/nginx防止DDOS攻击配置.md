---
title: "nginx防止DDOS攻击配置"
categories: [ "Linux" ]
tags: [ "nginx","ddos" ]
draft: false
slug: "nginx-to-prevent-ddos-attack-configuration"
date: "2013-09-16 09:08:00"
---

防御DDOS是一个系统工程，攻击花样多，防御的成本高瓶颈多，防御起来即被动又无奈。DDOS的特点是分布式，针对带宽和服务攻击，也就是四层流量攻击和七层应用攻击，相应的防御瓶颈四层在带宽，七层的多在架构的吞吐量。对于七层的应用攻击，我们还是可以做一些配置来防御的，例如前端是Nginx，主要使用nginx的`http_limit_conn和http_limit_req` 模块来防御。ngx_http_limit_conn_module 可以限制单个IP的连接数，`ngx_http_limit_req_module` 可以限制单个IP每秒请求数，通过限制连接数和请求数能相对有效的防御CC攻击。下面是配置方法：

## 一. 限制每秒请求数

ngx_http_limit_req_module模块通过漏桶原理来限制单位时间内的请求数，一旦单位时间内请求数超过限制，就会返回503错误。配置需要在两个地方设置：

    nginx.conf的http段内定义触发条件，可以有多个条件
    在location内定义达到触发条件时nginx所要执行的动作

例如：


<!--more-->


    http {
        limit_req_zone $binary_remote_addr zone=one:10m rate=10r/s; //触发条件，所有访问ip 限制每秒10个请求
        ...
        server {
            ...
            location  ~ \.php$ {
                limit_req zone=one burst=5 nodelay;   //执行的动作,通过zone名字对应
                   }
               }
         }

参数说明：

    $binary_remote_addr  二进制远程地址
    zone=one:10m    定义zone名字叫one，并为这个zone分配10M内存，用来存储会话（二进制远程地址），1m内存可以保存16000会话
    rate=10r/s;     限制频率为每秒10个请求
    burst=5         允许超过频率限制的请求数不多于5个，假设1、2、3、4秒请求为每秒9个，那么第5秒内请求15个是允许的，反之，如果第一秒内请求15个，会将5个请求放到第二秒，第二秒内超过10的请求直接503，类似多秒内平均速率限制。
    nodelay         超过的请求不被延迟处理，设置后15个请求在1秒内处理。

## 二.限制IP连接数

ngx_http_limit_conn_module的配置方法和参数与http_limit_req模块很像，参数少，要简单很多

    http {
        limit_conn_zone $binary_remote_addr zone=addr:10m; //触发条件
        ...
        server {
            ...
            location /download/ {
                limit_conn addr 1;    // 限制同一时间内1个连接，超出的连接返回503
                    }
               }
         }

## 三.白名单设置

http_limit_conn和http_limit_req模块限制了单ip单位时间内的并发和请求数，但是如果Nginx前面有lvs或者haproxy之类的负载均衡或者反向代理，nginx获取的都是来自负载均衡的连接或请求，这时不应该限制负载均衡的连接和请求，就需要geo和map模块设置白名单：

    geo $whiteiplist  {
            default 1;
            10.11.15.161 0;
        }
    map $whiteiplist  $limit {
            1 $binary_remote_addr;
            0 "";
        }
    limit_req_zone $limit zone=one:10m rate=10r/s;
    limit_conn_zone $limit zone=addr:10m;

geo模块定义了一个默认值是1的变量whiteiplist，当在ip在白名单中，变量whiteiplist的值为0，反之为1
如果在白名单中--> whiteiplist=0 --> $limit="" --> 不会存储到10m的会话状态（one或者addr）中 --> 不受限制
反之，不在白名单中 --> whiteiplist=1 --> $limit=二进制远程地址 -->存储进10m的会话状态中 --> 受到限制
四.测试

使用ab命令来模拟CC攻击，http_limit_conn和http_limit_req模块要分开测试，同时注意http_limit_conn模块只统计正在被处理的请求（这些请求的头信息已被完全读入）所在的连接。如果请求已经处理完，连接没有被关闭时，是不会被统计的。这时用netstat看到连接数可以超过限定的数量，不会被阻止。

    ab -n 请求数 -c 并发 http://10.11.15.174/i.php

如果被阻止前台会返回503，同时在nginx的error_log中会看到如下错误日志：
被限制连接数：

    2015/01/28 14:20:26 [error] 4107#0: *65525 limiting connections by zone "addr", client: 10.11.15.161, server: , request: "GET /i.php?=PHPE9568F35-D428-11d2-A769-00AA001ACF42 HTTP/1.1", host: "10.11.15.174", referrer: "http://10.11.15.174/i.php"

被限制请求数：

    2015/01/28 14:18:59 [error] 4095#0: *65240 limiting requests, excess: 5.772 by zone "one", client: 10.11.15.161, server: , request: "GET /i.php?=PHPE9568F34-D428-11d2-A769-00AA001ACF42 HTTP/1.1", host: "10.11.15.174", referrer: "http://10.11.15.174/i.php"

## 五.其它一些防CC的方法

### 1.Nginx模块 ModSecurity、http_guard、ngx_lua_waf

    ModSecurity 应用层WAF，功能强大，能防御的攻击多，配置复杂
    ngx_lua_waf 基于ngx_lua的web应用防火墙，使用简单，高性能和轻量级
    http_guard 基于openresty

### 2.软件+Iptables

fail2ban 通过分析日志来判断是否使用iptables拦截
DDoS Deflate 通过netstat判断ip连接数，并使用iptables屏蔽

开头说过抗DDOS是一个系统工程，通过优化系统和软件配置，只能防御小规模的CC攻击，对于大规模攻击、四层流量攻击、混合攻击来说，基本上系统和应用软件没挂，带宽就打满了。下面是我在工作中使用过的防御DDOS的方式：

高防服务器和带流量清洗的ISP 通常是美韩的服务器，部分ISP骨干供应商有流量清洗服务，例如香港的PCCW。通常可以防御10G左右的小型攻击

流量清洗服务 例如：akamai(prolexic),nexusguard 我们最大受到过80G流量的攻击，成功被清洗，但是费用非常贵

CDN 例如：蓝讯 网宿 cloudflare 等，CDN针对DDOS的分布式特点，将流量引流分散，同时对网站又有加速作用，效果好，成本相对低。

总结一下：发动攻击易，防御难。七层好防，四层难防；小型能防，大型烧钱

具体系统设置和nginx更好的防御CC方法，见nginx防御ddos第二篇： http://www.52os.net/articles/nginx-anti-ddos-setting-2.html

参考文章：
[http://nginx.org/en/docs/http/ngx_http_limit_req_module.html](http://nginx.org/en/docs/http/ngx_http_limit_req_module.html)
[http://www.nginx.cn/446.html](http://www.nginx.cn/446.html)
[http://www.ttlsa.com/nginx/nginx-limited-connection-number-ngx_http_limit_conn_module-module/](http://www.ttlsa.com/nginx/nginx-limited-connection-number-ngx_http_limit_conn_module-module/)


