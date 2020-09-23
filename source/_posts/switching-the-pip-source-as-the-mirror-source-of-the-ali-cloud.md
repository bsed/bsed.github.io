---
title: 切换PIP源为阿里云的镜像源
date: 2016-03-02 15:06:00
updated: 2018-08-02 15:07:59
tags: 
- debian
- bash
categories: 
- linux

---
### 1. 切换PIP源为阿里云的镜像源

[官方帮助教程](http://mirrors.aliyun.com/help/pypi)

编辑pip配置文件:

`vim ~/.pip/pip.conf`

然后写入如下内容：


<!--more-->


```bash
[global]
index-url = http://mirrors.aliyun.com/pypi/simple/

[install]
trusted-host = mirrors.aliyun.com
```

### 2. Ubuntu将默认的python版本换为python3.X

Ubuntu 18.04自带了python2.7和python3.5，且默认版本为python2.7，最近一直使用python3.×版本所以需讲python默认版本修改为3.5。

可以使用`ls /usr/bin | grep python`来查看目前存在的python版本

然后设置软链接，讲python3.5设为默认：

`sudo ln -s /usr/bin/python3.5 /usr/local/bin/python`

此时python3.5已经成为默认。

![aliyun_pip_mirror.png][1]

**然后此时需要注意的就是，如果以后主要在3.×的环境下工作，请安装对应3.×的包。**

```bash
sudo apt install python3-pip
pip3 install package_name
```

------

### **3. Ubuntu设置ssh密钥登录并关闭ssh密码登录**

**连网的机器安全第一，所以ssh的安全性就是重中之重了。使用ssh密钥登录，并关闭密码认证是有效的防止机器被入侵的方式。**

Ubuntu设置ssh密钥登录的步骤如下：

1.首先安装ssh

`sudo apt install ssh`

2.制作密钥对

`ssh-keygen`

你应该会看到如下的内容：

```bash
Generating public/private rsa key pair.
Enter file in which to save the key(/home/user/.ssh/id_rsa):

```

3.将公钥写入到对应文件里

```bash
cd ~/.ssh
cat id_rsa.pug >> authorized_keys
```

然后确保对应文件的权限是正确的:

```bash
chmod 600 authorized_keys
chmod 700 ~/.ssh
```

4.设置ssh配置文件，打开密钥登录并关闭密码登录

编辑`/etc/ssh/sshd_config`文件，确定如下设置：

```bash
RSAAuthentication yes
PubkeyAuthentication yes
```

当你确定可以使用密钥方式登录成功之后，再禁用密码登录：

`PasswordAuthentication no`

**记得如果这些修改项是被注释掉的，记得把注释取消。**

5.重启ssh服务

`service sshd restart`

6.关于[putty](https://the.earth.li/~sgtatham/putty/latest/w64/putty-64bit-0.69-installer.msi)使用id_rsa的一点儿说明

> putty是不能直接使用id_rsa私钥的，需要使用[puttygen](https://the.earth.li/~sgtatham/putty/latest/w64/puttygen.exe)转换。

首先从服务器下载id_rsa到本地，然后使用puttygen载入，然后选择Save private key讲私钥保存为putty能用的.ppk文件。然后就跟一般的putty使用key登录一样了。

**Linux下递归改变目录及其子目录属性**

加上－R(大写, regression)参数即可。

比如，

改变文件或目录所属的组, - R 递归式地改变指定目录及其下的所有子目录和文件的属组

将当前目录及其子目录和文件的属组改为 kelvin

```bash
chgrp -R kelvin *
```

改变文件或目录的访问权限, -R 递归式地改变指定目录及其下的所有子目录和文件的访问权限

将当前目录及其子目录和文件的访问权限改为属组下所有人可读，可写，可执行

```bash
chmod -R 777 *
```


  [1]: https://imgs.gnux.cn/usr/uploads/2018/08/3499342859.png