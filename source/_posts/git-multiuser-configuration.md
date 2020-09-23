---
title: GIT下实现多用户配置，Github、Gitlab 各一个
date: 2018-07-26 09:13:00
updated: 2018-07-26 17:16:47
tags: 
- javascript
- vue
- transation
categories: 
- js

---
公司配置了自己的私有gitlab ssh，同时学校有课题需要协作，于是参考一些文档做了多用户配置。
## 配置文件
在.ssh/文件夹下生成github.com对应的私钥公钥
执行命令 `ssh-keygen -t rsa -C id_rsa_github` 创建github对应的sshkey
```bash
ssh-keygen -t rsa -C id_rsa_github
```


<!--more-->


同样的方式生产gitlab的私钥公钥
执行命令 `ssh-keygen -t rsa -C id_rsa_gitlab` 创建gitlab对应的sshkey
```bash
ssh-keygen -t rsa -C id_rsa_gitlab
```
把github对应的公钥和gitlab对应的公钥上传到服务器

在.ssh目录创建config文本文件并完成相关配置
```
HostName 　　　　　　　   这个是真实的域名地址
IdentityFile 　　　　　　　  这里是id_rsa的地址
PreferredAuthentications   配置登录时用什么权限认证--可设为publickey,password publickey,keyboard-interactive等
User 　　　　　　　　　　　配置使用用户名
例如下

```bash
#配置github.com
Host github.com                 
    HostName github.com
    IdentityFile C:\\Users\\\Administrator\\.ssh\\id_rsa_github
    PreferredAuthentications publickey
    User username1
#配置gitlab.com 
Host gitlab.com
    HostName gitlab.com
    IdentityFile C:\\Users\\\Administrator\\.ssh\\id_rsa_gitlab
    PreferredAuthentications publickey
    User username2
```

测试命令测试是否配置成功


```bash
ssh -T git@github.com
```
## 常见问题
出现Could not open a connection to your authentication agent

## 解决方法有三种

（a) 先输入ssh-agent bash，然后再输入`ssh-add ~/.ssh/id_rsa_github`；

（b）先输入eval $(ssh-agent)，然后输入`ssh-add ~/.ssh/id_rsa_github`；

（c）使用Git GUI生成密钥，密钥会自动被加进ssh-agent中；