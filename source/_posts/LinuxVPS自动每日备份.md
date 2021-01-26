---
title: "LinuxVPS自动每日备份"
categories: [ "Java" ]
tags: [ "linux","Backup" ]
draft: false
slug: "linuxvps-automatic-daily-backup"
date: "2016-11-18 20:57:00"
---

最近用的是Buyvm的VPS，性价比很给力，可是因为他家是ONEMAN运营，怕跑路。钱的损失不重要，才几美元。可数据呢？如果丢了，就灰常有趣了。

此文介绍一种，每天自动备份网站以及数据库文件，发送EMAIL到邮箱，并上传网站和数据加文件到FTP空间，自动删除旧备份的方法。

首先安装EMAIL发送组件：
```bash
yum install sendmail mutt
```
脚本下载地址：https://raw.githubusercontent.com/bsed/shellcool/master/AutoBackupToFtp.sh

脚本代码如下（注意修改FTP服务器地址用户名密码）：


<!--more-->


```bash
#!/bin/bash
#你要修改的地方从这里开始
MYSQL_USER=root                             #mysql用户名
MYSQL_PASS=123456                      #mysql密码
MAIL_TO=cat@hostloc.com                 #数据库发送到的邮箱
FTP_USER=cat                              #ftp用户名
FTP_PASS=123456                         #ftp密码
FTP_IP=gnux.cn                          #ftp地址
FTP_backup=backup                          #ftp上存放备份文件的目录,这个要自己得ftp上面建的
WEB_DATA=/home/www                          #要备份的网站数据
#你要修改的地方从这里结束

#定义数据库的名字和旧数据库的名字
DataBakName=Data_$(date +"%Y%m%d").tar.gz
WebBakName=Web_$(date +%Y%m%d).tar.gz
OldData=Data_$(date -d -5day +"%Y%m%d").tar.gz
OldWeb=Web_$(date -d -5day +"%Y%m%d").tar.gz
#删除本地3天前的数据
rm -rf /home/backup/Data_$(date -d -3day +"%Y%m%d").tar.gz /home/backup/Web_$(date -d -3day +"%Y%m%d").tar.gz
cd /home/backup
#导出数据库,一个数据库一个压缩文件
for db in `/usr/local/mysql/bin/mysql -u$MYSQL_USER -p$MYSQL_PASS -B -N -e 'SHOW DATABASES' | xargs`; do
    (/usr/local/mysql/bin/mysqldump -u$MYSQL_USER -p$MYSQL_PASS ${db} | gzip -9 - > ${db}.sql.gz)
done
#压缩数据库文件为一个文件
tar zcf /home/backup/$DataBakName /home/backup/*.sql.gz
rm -rf /home/backup/*.sql.gz
#发送数据库到Email,如果数据库压缩后太大,请注释这行
echo "主题:数据库备份" | mutt -a /home/backup/$DataBakName -s "内容:数据库备份" $MAIL_TO
#压缩网站数据
tar zcf /home/backup/$WebBakName $WEB_DATA
#上传到FTP空间,删除FTP空间5天前的数据
ftp -v -n $FTP_IP << END
user $FTP_USER $FTP_PASS
type binary
cd $FTP_backup
delete $OldData
delete $OldWeb
put $DataBakName
put $WebBakName
bye
END
```
给脚本添加执行权限：
```bash
chmod +x /root/AutoBackupToFtp.sh
```
利用系统crontab实现每天自动运行：
```bash
crontab -e
```
输入以下内容：
```bash
00 00 * * * /root/AutoBackupToFtp.sh
```
其中00 00为时间分/小时，可自行修改，例如：30 12 ***，就是每天12.30运行这个脚本。

以下为lftp备份脚本：
```bash
#!/bin/bash
#定义数据库的名字和旧数据库的名字
DataBakName=Data_$(date +"%Y%m%d").tar.gz
WebBakName=Web_$(date +%Y%m%d).tar.gz
#删除本地3天前的数据
rm -rf /home/backup/Data_$(date -d -3day +"%Y%m%d").tar.gz /home/backup/Web_$(date -d -3day +"%Y%m%d").tar.gz
#导出mysql数据库
/usr/local/mysql/bin/mysqldump -uroot -ppassword --databases db1 > /home/backup/databackup.sql
#压缩数据库
tar zcf /home/backup/$DataBakName /home/backup/databackup.sql
rm -rf /home/backup/databackup.sql
#压缩网站数据
tar zcvf /home/backup/$WebBakName /home/wwwroot
#使用lftp同步备份目录
lftp -u user,password -e "mirror -R --only-newer /home/backup /backup" ftp.yoursite.cn
exit
END
```