---
title: "ga.giuem.cn 博主 ga已挂，部署自有代理"
categories: [ "日常" ]
tags: [ "ga","giuem" ]
draft: false
slug: "ga-giunu"
date: "2019-07-16 09:41:00"
---

ga.giuem.cn 博主 ga已挂，部署自有代理

可以用浏览器打开 `https://ga.gnux.cn/ping`，判断博主ga是否已经挂掉哦!, 没挂掉效果如下：

![40925-afmx9gv5405.png](https://imgs.gnux.cn/usr/uploads/2019/07/2758136002.png)
笔者操作系统为： 
```
Distributor ID: Debian
Description:    Debian GNU/Linux 7.0 (wheezy)
Release:        7.0
Codename:       wheezy
```


<!--more-->


使用的是 [lnmp.sh](https://lnmp.org/) 部署程序：
```
root@10-8-58-166:~/src# lnmp
+-------------------------------------------+
|    Manager for LNMP, Written by Licess    |
+-------------------------------------------+
|              https://lnmp.org             |
+-------------------------------------------+
Usage: lnmp {start|stop|reload|restart|kill|status}
Usage: lnmp {nginx|mysql|mariadb|php-fpm|pureftpd} {start|stop|reload|restart|kill|status}
Usage: lnmp vhost {add|list|del}
Usage: lnmp database {add|list|edit|del}
Usage: lnmp ftp {add|list|edit|del|show}
Usage: lnmp ssl add
Usage: lnmp {dnsssl|dns} {cx|ali|cf|dp|he|gd|aws}
```

具体如下：

```
<script>if(navigator.doNotTrack!=='1'||window.doNotTrack!=='1'){!function(a,b,c,d,e){var f=c.screen,g=encodeURIComponent,h=["ga="+a,"dt="+g(d.title),"dr="+g(d.referrer),"ul="+(e.language||e.browserLanguage||e.userLanguage),"sd="+f.colorDepth+"-bit","sr="+f.width+"x"+f.height,"vp="+Math.max(d.documentElement.clientWidth,c.innerWidth||0)+"x"+Math.max(d.documentElement.clientHeight,c.innerHeight||0),"z="+ +new Date];c.__ga_img=new Image,c.__ga_img.src=b+"?"+h.join("&")}("UA-102970207-1","https://ga.giuem.com",window,document,navigator,location)}</script>
```
修改为：
```bash
<script>if(navigator.doNotTrack!=='1'||window.doNotTrack!=='1'){!function(a,b,c,d,e){var f=c.screen,g=encodeURIComponent,h=["ga="+a,"dt="+g(d.title),"dr="+g(d.referrer),"ul="+(e.language||e.browserLanguage||e.userLanguage),"sd="+f.colorDepth+"-bit","sr="+f.width+"x"+f.height,"vp="+Math.max(d.documentElement.clientWidth,c.innerWidth||0)+"x"+Math.max(d.documentElement.clientHeight,c.innerHeight||0),"z="+ +new Date];c.__ga_img=new Image,c.__ga_img.src=b+"?"+h.join("&")}("UA-102970207-1","https://ga.gnux.cn",window,document,navigator,location)}</script>
```
自己的nginx 反向代理：


<!--more-->


`UA-102970207-1`: 修改为自己的 google ga 账号
`https://ga.giuem.com`:  修改为我的 `ga.gnux.cn` （**由于资金不足**随时可能挂掉，望悉知）
```conf
server
    {
        listen 80;
        #listen [::]:80;
        server_name ga.gnux.cn ;
        index index.html index.htm index.php default.html default.htm default.php;
        root  /home/wwwroot/ga.gnux.cn;
        location / {
                try_files $uri @backend;
        }
        location @backend {
                proxy_set_header X-Forwarded-For $remote_addr;
                proxy_set_header Host $http_host;
                proxy_pass http://127.0.0.1:8181;
        }
        #include rewrite/none.conf;
        #error_page   404   /404.html;

        # Deny access to PHP files in specific directory
        #location ~ /(wp-content|uploads|wp-includes|images)/.*\.php$ { deny all; }

        #include enable-php.conf;

        location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$
        {
            expires      30d;
        }

        location ~ .*\.(js|css)?$
        {
            expires      12h;
        }

        location ~ /.well-known {
            allow all;
        }

        location ~ /\.
        {
            deny all;
        }

```

`ga-proxy_linux_amd64` 程序 添加到 `/etc/rc.local` 文件最后面

```bash
/root/src/ga-proxy_linux_amd64 -i 0.0.0.0 -p 8181
```

用 vi 打开 `/etc/iptables.up.rules`, 在 合适位置添加 如下代码 打开 8181:
```
-A INPUT -p udp -m state --state NEW -m udp --dport 8181 -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 8181 -j ACCEPT
```
