---
title: "修改SSH端口+禁止ROOT登陆 [运维篇]"
categories: [ "Linux" ]
tags: [ "linux","ssh" ]
draft: false
slug: "modify-ssh-port-to-prohibit-root-login-operations-and-maintenance-chapter"
date: "2018-11-22 16:12:00"
---

> 其实本身Linux已经很安全了，但是如果密码设置的不够复杂，如果说小写+数字共12位以下，你的SSH还用的默认端口，那强力一点的黑客用不上半小时，就能暴力破解你的密码。所以，最好的方法就是修改掉SSH的端口。

## 一、修改文件：/etc/ssh/sshd_config

Port 22 #在第三行或第四行，如果前面有井号，请删除，修改为65534以下即可
可在远程连接中用vi命令，或sftp下载到本地修改，修改后使用以下命令重启ssh服务


<!--more-->


```bash
/etc/init.d/sshd restart #centos系统，重启ssh服务命令
/etc/init.d/ssh restart #debian/ubuntu系统，重启ssh服务命令
```
## 二、更加安全的设置，禁止ROOT登陆，采用小号登陆再切换ROOT（此方法不能用SFTP上传文件）
```bash
useradd kelvin #新建一个小号
passwd kelvin #给小号设置密码，需要输入完全相同的二次，注意提示
vi /etc/ssh/sshd_config #修改的文件还是这个
PermitRootLogin yes #把yes，改成no，保存退出，并重启SSH服务（上面有重启命令）
```
切记，如果没有新建小号，或小号密码设置错误，你又禁了ROOT，那你只能重启系统或回滚快照，再也登陆不了。

如果不是极度需要安全环境，并且，还需要使用SFTP管理文件，那改掉端口就行了。
