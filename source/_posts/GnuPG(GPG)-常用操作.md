---
title: "GnuPG(GPG) 常用操作"
categories: [ "Linux" ]
tags: [ "加密","gpg" ]
draft: false
slug: "gnupg-gpg-common-operation"
date: "2015-08-21 16:53:00"
---

![screen-shot-2012-04-30-at-9-02-19-pm.png][1]
关于GPG，可以参考[这里](http://www.pgpi.org/doc/pgpintro/)。

### 列出keys

```sh
# gpg --list-keys
/root/.gnupg/pubring.gpg
------------------------
pub   2048R/98681A63 2014-07-15
uid                  yinye (yinye's key) <yy@xspring.net>
sub   2048R/5A1B2B28 2014-07-15
```

### 导出public key


<!--more-->


这样，别人就可以导入，然后验证你签名的rpm package了。

```sh
# gpg --export --armor yy@xspring.net > yy.asc
```

### 备份private key

```sh
# gpg --export-secret-keys --armor yy@xspring.net > yy_private.asc
```

### 检查rpm包的签名

```sh
# rpm -K ceph-deploy-1.5.9-0.noarch.rpm 
ceph-deploy-1.5.9-0.noarch.rpm: RSA sha1 ((MD5) PGP) md5 NOT OK (MISSING KEYS: (MD5) PGP#98681a63)
```

检查rpm包的完整性（下载过程中是否损坏） ceph-deploy :[https://ceph.com/rpm-testing/rhel7/noarch/](https://ceph.com/rpm-testing/rhel7/noarch/)

```sh
# rpm -K --nosignature ceph-deploy-1.5.9-0.noarch.rpm 
ceph-deploy-1.5.9-0.noarch.rpm: sha1 md5 OK
```
注意了；如果你用的是Ubuntu 系统的话，需要安装rpm， 命令　
```sh
sudo apt-get install rpm
```
<del>由于是.rpm后缀，　所以你也需要安装 alien rpm 包转换工具 `sudo apt-get install alien`</del>
<del>然后做rpm包 到deb包的转换 命令：　`# sudo alien --scripts /tmp/ceph-deploy-1.5.9-0.noarch.rpm `</del>
直接使用命令安装ceph-deploy 文件完整名字如下`ceph-deploy_1.5.20-0ubuntu1_all.deb`命令如下：
```sh
apt-get install ceph-deploy
```

### 导入public key

```sh
rpm --import 'https://ceph.com/git/?p=ceph.git;a=blob_plain;f=keys/autobuild.asc'
```

### 列出public key

```sh
# rpm -qa gpg-pubkey*
gpg-pubkey-03c3951a-51149b56
gpg-pubkey-baadae52-49beffa4
gpg-pubkey-c105b9de-4e0fd3a3
gpg-pubkey-17ed316d-4fb96ee8
gpg-pubkey-0608b895-4bd22942

# rpm -qa gpg-pubkey* --qf "%{name}-%{version}-%{release}-%{summary}\n"
gpg-pubkey-03c3951a-51149b56-gpg(Ceph automated package build (Ceph automated package build) <sage@newdream.net>)
gpg-pubkey-baadae52-49beffa4-gpg(elrepo.org (RPM Signing Key for elrepo.org) <secure@elrepo.org>)
gpg-pubkey-c105b9de-4e0fd3a3-gpg(CentOS-6 Key (CentOS 6 Official Signing Key) <centos-6-key@centos.org>)
gpg-pubkey-17ed316d-4fb96ee8-gpg(Ceph Release Key <sage@newdream.net>)
gpg-pubkey-0608b895-4bd22942-gpg(EPEL (6) <epel@fedoraproject.org>)
```

### 查看public key的详细信息

```sh
# rpm -qi gpg-pubkey-03c3951a-51149b56
```

### 主要参考

* [How to Import RPM GPG Key](http://www.bashguru.com/2011/10/how-to-import-rpm-gpg-key.html)

* https://fedoraproject.org/wiki/Creating_GPG_Keys
* [http://www.ruanyifeng.com/blog/2013/07/gpg.html](http://www.ruanyifeng.com/blog/2013/07/gpg.html)

* [http://lignux.com/crear-claves-gpg-desde-la-terminal-en-gnulinux/](http://lignux.com/crear-claves-gpg-desde-la-terminal-en-gnulinux/)
  [1]: https://imgs.gnux.cn/usr/uploads/2015/08/1342144685.png