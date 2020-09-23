---
title: Linux下发送邮件的使用技巧 
date: 2014-11-13 14:36:00
updated: 2014-11-14 23:03:06
tags: 
- rweibo
categories: 
- rails

---
sendEmail的主页[http://caspian.dotconf.net/menu/Software/SendEmail/](http://caspian.dotconf.net/menu/Software/SendEmail/)
下载地址[http://caspian.dotconf.net/menu/Software/SendEmail/sendEmail-v1.56.tar.gz](http://caspian.dotconf.net/menu/Software/SendEmail/sendEmail-v1.56.tar.gz)


<!--more-->


    #tar –zxvf sendEmail-v1.56.tar.gz
    #cd sendEmail-v1.56
    #cp sendEmail /usr/local/bin

然后给确认确实它具有执行权限

    ll /usr/local/bin/sendEmail 
    -rwxr-xr-x 1 root root 77882 11-03 14:23 /usr/local/bin/sendEmail

这样程序就装好了,使用也很简单.直接运行sendEmail就会显示详细的用法
先看一个典型的例子

解释:

 1. -f 表示发送者的邮箱
 2. -t 表示接收者的邮箱
 3. -s 表示SMTP服务器的域名或者ip
 4. -u 表示邮件的主题
 5. -xu 表示SMTP验证的用户名
 6. -xp 表示SMTP验证的密码(注意,这个密码貌似有限制,例如我用d!5neyland就不能被正确识别)
 7. -m 表示邮件的内容

如果你不带-m参数的话,就会提示你自行输入

    Reading message body from STDIN because the ‘-m’ option was not used.
    If you are manually typing in a message:
    - First line must be received within 60 seconds.
    - End manual input with a CTRL-D on its own line

输入完成后使用CTRL-D来结束
当然我们也可以将一个文件的内容作为邮件的正文发出去的
那么就可以使用:

    cat 文件名 | /usr/local/bin/sendEmail –f –t –s smtp.163.com –u "subject” –xu sender –xp 123456 –m happy

有关sendEmail的用法就讲到这里
既然nagios要使用sendEmail来发警告邮件,那么就要修改commands.cfg中关于发邮件的命令的定义,我们现在来修改notify-by-email这个命令,如下(注意其中粗体的部分)

    # 'notify-by-email' command definition
    define command{
            command_name    notify-by-email
            command_line    /usr/bin/printf "%b" "***** Nagios 2.9 *****\n\nNotification Type: $NOTIFICATIONTYPE$\n\nService: $SERVICEDESC$\nHost: $HOSTALIAS$\nAddress: $HOSTADDRESS$\nState: $SERVICESTATE$\n\nDate/Time: $LONGDATETIME$\n\nAdditional Info:\n\n$SERVICEOUTPUT$" | /usr/local/bin/sendEmail -f -t $CONTACTEMAIL$ -s smtp.163.com -u "** $NOTIFICATIONTYPE$ alert - $HOSTALIAS$/$SERVICEDESC$ is $SERVICESTATE$ **" -xu sender -xp 123456
        }

**注**:其实sendEmail是一个十分有用的程序,我们在这个地方用了它,其实别的地方也可以用,典型的好处就是你不需要每台机器都装sendmail,开启smtp服务.直接用现成的一台邮件服务器就行了,这无疑很大的加强了系统的安全性,也节约了资源.