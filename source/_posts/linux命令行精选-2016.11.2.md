---
title: "linux命令行精选 2016.11.2"
categories: [ "Linux" ]
tags: [ "linux","shell" ]
draft: false
slug: "linux-command-line-select-2016112"
date: "2016-11-02 21:36:00"
---

显示PATH每行一个

```bash
printf ${PATH//:/\\n}
```

vim的粘贴切换，按F9切换粘贴模式的开/关,插入模式和命令模式都有用

```bash
:set pt=<f9> 
```
查找不符合ISO 9660（一种光盘文件系统） Level 2的文件名和文件夹名


<!--more-->


```bash
find . -regextype posix-extended -not -regex '.*/[A-Za-z_]*([.][A-Za-z_]*)?' 
```
生成一个十位的随机密码

```bash
pwgen -B -y 10
```
从firefox中提取cookie

```bash
echo ".mode tabs select host, case when host glob '.*' then 'TRUE' else 'FALSE' end, path, case when isSecure then 'TRUE' else 'FALSE' end, expiry, name, value from moz_cookies;" | sqlite3 ~/.mozilla/firefox/*.default/cookies.sqlite
```

当服务器起来后自动ssh

```bash
var=host ;while ! nc -zw 1 $var 22;do sleep 1; done ; ssh user@$var
```

通过ssh加wireshark来分析远程主机的流量
```bash
ssh root@server.com 'tshark -f "port !22" -w -' | wireshark -k -i -
```

```bash
/sbin/ip -f inet addr | sed -rn 's/.*inet ([^ ]+).*(eth[[:digit:]]*(:[[:digit:]]+)?)/\2 \1/p' | column -t
```

读取设备的ip
```bash
svn st | awk ' {if ( $1 == "?" ){print $1="",$0}} ' | sed -e 's/^[ \t]*//' | sed 's/ /\\ /g' |  perl -ne '`svn add ${1}@` if /(.*)(@*)(.*)/'
```

显示所有TODO
```
grep -rnA 10 TODO *
```

列出使用某个端口的服务
```bash
grep '\<110/' /etc/services; grep '\b110/' /etc/services
```

从子目录中检索文件并复制到当前目录
```bash
find ./ -iname '*avi' -exec cp  {} ./ \;
```

手动轮转日志
```bash
or i in `seq 1 12| tac` ; do mv access_log.{$i,$((i+1))}.gz ; done
```

