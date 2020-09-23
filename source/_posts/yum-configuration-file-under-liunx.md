---
title:  Liunx下配置yum配置文件
date: 2014-03-16 08:18:00
updated: 2015-10-25 08:19:42
tags: 
- vim
- gvim
categories: 
- vim

---
 1.确保RHEL5中已经安装了yum

[root@lvs-master ~]# rpm -qa |grep yum
yum-metadata-parser-1.1.2-3.el5
yum-updatesd-0.9-2.el5
yum-3.2.22-26.el5
yum-security-1.1.16-13.el5
yum-rhn-plugin-0.5.4-15.el5

    cd /etc/yum.repos.d/
    mv CentOS-Base.repo CentOS-Base.repo.bak
    wget http://docs.linuxtone.org/soft/lemp/CentOS-Base.repo


<!--more-->


运行上面三行代码,再vim CentOS-Base.repo 该文件内容如下：

    [base]
    name=CentOS-5 - Base
    baseurl=http://centos.ustc.edu.cn/centos/5/os/$basearch/
    gpgcheck=1
    gpgkey=http://mirror.centos.org/centos/RPM-GPG-KEY-CentOS-5
    
    #released updates
    [update]
    name=CentOS-5 - Updates
    baseurl=http://centos.ustc.edu.cn/centos/5/updates/$basearch/
    gpgcheck=1
    gpgkey=http://mirror.centos.org/centos/RPM-GPG-KEY-CentOS-5
    
    #packages used/produced in the build but not released
    [addons]
    name=CentOS-5 - Addons
    baseurl=http://centos.ustc.edu.cn/centos/5/addons/$basearch/
    gpgcheck=1
    gpgkey=http://mirror.centos.org/centos/RPM-GPG-KEY-CentOS-5
    #additional packages that may be useful
    [extras]
    name=CentOS-5 - Extras
    baseurl=http://centos.ustc.edu.cn/centos/5/extras/$basearch/
    gpgcheck=1
    gpgkey=http://mirror.centos.org/centos/RPM-GPG-KEY-CentOS-5
    
    #additional packages that extend functionality of existing packages
    [centosplus]
    name=CentOS-5 - Plus
    baseurl=http://centos.ustc.edu.cn/centos/5/centosplus/$basearch/
    gpgcheck=1
    enabled=0
    gpgkey=http://mirror.centos.org/centos/RPM-GPG-KEY-CentOS-5
    
    #contrib - packages by Centos Users
    [contrib]
    name=CentOS-5 - Contrib
    baseurl=http://centos.ustc.edu.cn/centos/5/contrib/$basearch/
    gpgcheck=1
    enabled=0
    gpgkey=http://mirror.centos.org/centos/RPM-GPG-KEY-CentOS-5
    
    #packages in testing
    [testing]
    name=CentOS-5 - Testing
    baseurl=http://centos.ustc.edu.cn/centos/5/testing/$basearch/
    gpgcheck=1
    enabled=0
    gpgkey=http://mirror.centos.org/centos/RPM-GPG-KEY-CentOS-5

看到上述内容就是成功了

再运行:`yum -y update` 测试就行了 

