---
title: "ssh免密码登录"
categories: [ "Java" ]
tags: [ "ssh","bash" ]
draft: false
slug: "ssh-password-free-login"
date: "2014-02-06 22:01:00"
---

配置SSH无密码登录需要3步：

1.生成公钥和私钥
2.导入公钥到认证文件,更改权限
3.测试

## 1.生成公钥和私钥
```bash
ssh-keygen -t dsa -P '' -f ~/.ssh/id_dsa
```


<!--more-->


默认在 `~/.ssh`目录生成两个文件：

 - id_rsa      ：私钥
 - id_rsa.pub  ：公钥

## 2.导入公钥到认证文件,更改权限
### 2.1 导入本机
```bash    
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys  
```
### 2.2 导入要免密码登录的服务器
首先将公钥复制到服务器
```bash
kelvin@gnux:~# scp .ssh/id_rsa.pub  root@192.168.1.102:/root/id_rsa.pub
```
然后，将公钥导入到认证文件，这一步的操作在服务器上进行
```bash
cat ~/id_rsa.pub >> ~/.ssh/authorized_keys 
```

### 2.3 在服务器上更改权限
``` 
    chmod 700 ~/.ssh
    chmod 600 ~/.ssh/authorized_keys  
```
   
## 3.测试
 
`ssh root@192.168.1.102`，第一次登录可能需要yes确认，之后就可以直接登录了。
 
一般以上几步就ok了，但我的仍要输入密码，

用root用户登陆查看系统的日志文件：`$tail /var/log/secure -n 20`
…………
Nov 19 22:36:05 bigdata sshd[1327]: Received disconnect from 192.168.1.101: 11: disconnected by user
Nov 19 22:36:05 bigdata sshd[1327]: pam_unix(sshd:session): session closed for user root
Nov 19 22:36:23 bigdata sshd[1345]: Connection closed by 192.168.1.101
Nov 19 22:37:02 bigdata sshd[1346]: Failed password for kelvin from 192.168.1.101 port 38796 ssh2
Nov 19 22:37:03 bigdata sshd[1346]: Failed password for kelvin from 192.168.1.101 port 38796 ssh2
Nov 19 22:37:03 bigdata sshd[1347]: Connection closed by 192.168.1.101
Nov 19 22:37:14 bigdata sshd[1348]: Accepted password for kelvin from 192.168.1.101 port 38804 ssh2
Nov 19 22:37:14 bigdata sshd[1348]: pam_unix(sshd:session): session opened for user kelvin by (uid=0)
Nov 20 09:23:01 bigdata sshd[1143]: Server listening on 0.0.0.0 port 22.
Nov 20 09:23:01 bigdata sshd[1143]: Server listening on :: port 22.
Nov 20 09:23:09 bigdata login: pam_unix(login:session): session opened for user root by LOGIN(uid=0)
Nov 20 09:23:09 bigdata login: ROOT LOGIN ON tty1
Nov 20 09:23:26 bigdata su: pam_unix(su:session): session opened for user kelvin by root(uid=0)
Nov 20 09:23:50 bigdata su: pam_unix(su:session): session opened for user root by root(uid=500)
Nov 20 09:27:48 bigdata sshd[1338]: Accepted password for root from 192.168.1.101 port 40796 ssh2
Nov 20 09:27:48 bigdata sshd[1338]: pam_unix(sshd:session): session opened for user root by (uid=0)
Nov 20 09:27:48 bigdata sshd[1338]: Received disconnect from 192.168.1.101: 11: disconnected by user
Nov 20 09:27:48 bigdata sshd[1338]: pam_unix(sshd:session): session closed for user root
Nov 20 09:28:53 bigdata sshd[1349]: Accepted publickey for root from 192.168.1.101 port 40820 ssh2
Nov 20 09:28:53 bigdata sshd[1349]: pam_unix(sshd:session): session opened for user root by (uid=0)
…………
提示/root/.ssh和 /root/.ssh/authorized_keys权限不对，修改如下：见2.3   
```
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys 
```
## 4.测试
    ssh host，第一次登录可能需要yes确认，之后就可以直接登录了。
