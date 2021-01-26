---
title: "Shadowsocks.org官网正确二维码编码方法"
categories: [ "日常" ]
tags: [ "shadowsocks5" ]
draft: false
slug: "shadowsock5-quick-guide"
date: "2011-12-21 11:19:00"
---

### Quick Guide ###


<!--more-->

#### Config File ####

Shadowsocks accepts `JSON` format configs like this:
    
    {
        "server":"my_server_ip",
        "server_port":8388,
        "local_port":1080,
        "password":"barfoo!",
        "timeout":600,
        "method":"table"
    }

Explanation of each field:
- server: your hostname or server IP (IPv4/IPv6).
- server_port: server port number.
- local_port: local port number.
- password: a password used to encrypt transfer.
- timeout: connections timeout in seconds.
- method: encryption method, "bf-cfb", "aes-256-cfb", "des-cfb", "rc4", etc. Default is table, which is not secure. "aes-256-cfb" is recommended.

#### URI and QR code ####

Shadowsocks for Android / iOS also accepts BASE64 encoded URI format configs:
    `ss://BASE64-ENCODED-STRING-WITHOUT-PADDING`
Where the plain URI should be:
    `ss://method:password@hostname:port`
For example, we have a server at `192.168.100.1:8888` using `bf-cfb` encryption method and password `test`. Then, with the plain URI `ss://bf-cfb:test@192.168.100.1:8888`, we can generate the BASE64 

### Try it yourself ###

#### Plain: ####


<input type="text" value="ss://bf-cfb:test@192.168.100.1:8888" />
encoded URI:
    `ss://YmYtY2ZiOnRlc3RAMTkyLjE2OC4xMDAuMTo4ODg4`
This URI can also be encoded to QR code. Then, just scan it with your Android / iOS devices:
![shadowsock_org1.png][1]
#### QR Code: ####
![shadowsock_org1.png][1]
------
原文：[http://shadowsocks.org/en/config/quick-guide.html](http://shadowsocks.org/en/config/quick-guide.html)


  [1]: https://imgs.gnux.cn/usr/uploads/2014/11/90072417.png