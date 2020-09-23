---
title: dpkg: error processing mysql-server (--configure)
date: 2016-11-19 16:34:00
updated: 2016-11-19 16:40:18
tags: 
- linux
- netstat
categories: 
- linux

---
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following packages will be REMOVED:
  mysql-community-client mysql-community-server
The following NEW packages will be installed:
  mysql-client-5.7 mysql-client-core-5.7 mysql-server-5.7
  mysql-server-core-5.7
The following packages will be upgraded:
  mysql-client mysql-common mysql-server
3 upgraded, 4 newly installed, 2 to remove and 0 not upgraded.
Need to get 19.6 MB of archives.
After this operation, 4,515 kB of additional disk space will be used.
Do you want to continue? [Y/n] Y
Get:1 http://mirrors.aliyun.com/ubuntu yakkety-security/main amd64 mysql-client all 5.7.16-0ubuntu0.16.10.1 [10.0 kB]
Get:2 http://mirrors.aliyun.com/ubuntu yakkety-security/main amd64 mysql-client-core-5.7 amd64 5.7.16-0ubuntu0.16.10.1 [6,950 kB]
```
Get:3 http://mirrors.aliyun.com/ubuntu yakkety-security/main amd64 mysql-server all 5.7.16-0ubuntu0.16.10.1 [10.2 kB]
Get:4 http://mirrors.aliyun.com/ubuntu yakkety-security/main amd64 mysql-server-core-5.7 amd64 5.7.16-0ubuntu0.16.10.1 [7,982 kB]
Get:5 http://mirrors.aliyun.com/ubuntu yakkety-security/main amd64 mysql-server-5.7 amd64 5.7.16-0ubuntu0.16.10.1 [2,753 kB]
Get:6 http://mirrors.aliyun.com/ubuntu yakkety-security/main amd64 mysql-common all 5.7.16-0ubuntu0.16.10.1 [17.5 kB]
Get:7 http://mirrors.aliyun.com/ubuntu yakkety-security/main amd64 mysql-client-5.7 amd64 5.7.16-0ubuntu0.16.10.1 [1,831 kB]
Fetched 19.6 MB in 29s (654 kB/s)                                              
Reading changelogs... Done
apt-listchanges: Mailing root: apt-listchanges: news for gnux
Preconfiguring packages ...
(Reading database ... 661995 files and directories currently installed.)
Preparing to unpack .../mysql-client_5.7.16-0ubuntu0.16.10.1_all.deb ...
Unpacking mysql-client (5.7.16-0ubuntu0.16.10.1) over (5.6.30-1ubuntu15.10) ...
(Reading database ... 661992 files and directories currently installed.)
Removing mysql-community-client (5.6.30-1ubuntu15.10) ...
Selecting previously unselected package mysql-client-core-5.7.
(Reading database ... 661951 files and directories currently installed.)
Preparing to unpack .../0-mysql-client-core-5.7_5.7.16-0ubuntu0.16.10.1_amd64.deb ...
Unpacking mysql-client-core-5.7 (5.7.16-0ubuntu0.16.10.1) ...
Preparing to unpack .../1-mysql-server_5.7.16-0ubuntu0.16.10.1_all.deb ...
Unpacking mysql-server (5.7.16-0ubuntu0.16.10.1) over (5.6.30-1ubuntu15.10) ...
(Reading database ... 661958 files and directories currently installed.)
Removing mysql-community-server (5.6.30-1ubuntu15.10) ...
update-alternatives: using /etc/mysql/my.cnf.fallback to provide /etc/mysql/my.cnf (my.cnf) in auto mode
Selecting previously unselected package mysql-server-core-5.7.
(Reading database ... 661851 files and directories currently installed.)
Preparing to unpack .../0-mysql-server-core-5.7_5.7.16-0ubuntu0.16.10.1_amd64.deb ...
Unpacking mysql-server-core-5.7 (5.7.16-0ubuntu0.16.10.1) ...
dpkg: error processing archive /tmp/apt-dpkg-install-bdTngr/0-mysql-server-core-5.7_5.7.16-0ubuntu0.16.10.1_amd64.deb (--unpack):
 trying to overwrite '/usr/share/mysql/bulgarian/errmsg.sys', which is also in package mysql-common 5.6.30-1ubuntu15.10
dpkg-deb: error: subprocess paste was killed by signal (Broken pipe)
Selecting previously unselected package mysql-server-5.7.
Preparing to unpack .../1-mysql-server-5.7_5.7.16-0ubuntu0.16.10.1_amd64.deb ...
Unpacking mysql-server-5.7 (5.7.16-0ubuntu0.16.10.1) ...
dpkg: error processing archive /tmp/apt-dpkg-install-bdTngr/1-mysql-server-5.7_5.7.16-0ubuntu0.16.10.1_amd64.deb (--unpack):
 trying to overwrite '/usr/share/mysql/docs/INFO_BIN', which is also in package mysql-common 5.6.30-1ubuntu15.10
dpkg-deb: error: subprocess paste was killed by signal (Broken pipe)
Preparing to unpack .../2-mysql-common_5.7.16-0ubuntu0.16.10.1_all.deb ...
Unpacking mysql-common (5.7.16-0ubuntu0.16.10.1) over (5.6.30-1ubuntu15.10) ...
dpkg: warning: mysql-common: conffile '/etc/mysql/conf.d/mysql.cnf' is not a plain file or symlink (= '/etc/mysql/conf.d/mysql.cnf')
Selecting previously unselected package mysql-client-5.7.
Preparing to unpack .../3-mysql-client-5.7_5.7.16-0ubuntu0.16.10.1_amd64.deb ...
Unpacking mysql-client-5.7 (5.7.16-0ubuntu0.16.10.1) ...
Errors were encountered while processing:
 /tmp/apt-dpkg-install-bdTngr/0-mysql-server-core-5.7_5.7.16-0ubuntu0.16.10.1_amd64.deb
 /tmp/apt-dpkg-install-bdTngr/1-mysql-server-5.7_5.7.16-0ubuntu0.16.10.1_amd64.deb
E: Sub-process /usr/bin/dpkg returned an error code (1)
```


**解决方法:**
删除mysql前 先删除一下 `/var/lib/mysql` 还有 `/etc/mysql`

```bash
sudo rm /var/lib/mysql/ -R
sudo rm /etc/mysql/ -R

sudo apt-get autoremove mysql* --purge
sudo apt-get remove apparmor

sudo apt-get install mysql-server mysql-common
```

成功如图所示：
![mysql5.7.png][1]


  [1]: https://imgs.gnux.cn/usr/uploads/2016/11/220989670.png