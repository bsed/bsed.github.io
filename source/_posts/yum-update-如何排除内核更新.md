---
title: "yum update 如何排除内核更新"
categories: [ "Linux" ]
tags: [ "bash" ]
draft: false
slug: "how-to-exclude-kernel-updates-by-yum-update"
date: "2019-01-16 08:23:00"
---

在`CentOS/RHEL/Fedora`下的Linux服务器使用 `yum update` 时命令如何排除选定的包呢？

Yum使用`/etc/yum/yum.conf`或`/etc/yum.conf`中的配置文件。您需要放置`exclude`指令来定义要更新或安装中排除的包列表。

## 使用`yum update`时，如何排除`内核包`？

打开/etc/yum.conf文件，输入：


<!--more-->


```bash
vi /etc/yum.conf
```

在`[main]`部分下面添加以下行，输入：

```bash
exclude=kernel*
```

最后，它应如下所示：

```bash
[ main ]
cachedir = /var/cache/yum 
keepcache = 0 
debuglevel = 2 
logfile = /var/log/yum.log 
distroverpkg = redhat-release 
tolerant = 1 
exactarch = 1 
obsoletes = 1 
gpgcheck = 1 
plugins = 1 
exclude =php* kernel*
```

保存并关闭文件。您现在可以照常使用yum命令，但不会安装某些软件包。

## 如何禁用排除？

```bash
yum --disableexcludes = all update`
`yum --disableexcludes = main install php`
`yum --disableexcludes = repoid install php httpd
```

这里：

- `all`：禁用所有排除
- `main`：禁用yum.conf中[main]中定义的排除
- `repoid`：禁用为给定repo id定义的排除
- `yum -exclude` 命令行选项

最后，您可以使用以下语法在命令行上跳过`yum`命令更新：

注意：上述语法将按名称排除特定包，或者从所有存储库的更新中排除。

```bash
yum --exclude=php\* update
yum --exclude=package\* update
yum --exclude=kernel\* update
yum -x 'kernel*' -x 'php*' update
```