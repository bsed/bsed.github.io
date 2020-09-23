---
title: 一键安装SSR服务器
date: 2017-06-12 12:56:00
updated: 2018-06-13 12:55:30
tags: 
- css
categories: 
- css

---
（来自https://www.91yun.org/archives/2079）

一键安装命令:

wget -N --no-check-certificate https://raw.githubusercontent.com/91yun/shadowsocks_install/master/shadowsocksR.sh && bash shadowsocksR.sh

安装完成后，显示：

Congratulations, ShadowsocksR install completed!

 Server IP:  你的服务器的ip

 Server Port:  7979(你所设置的端口号）

 Password:  你设置的密码

 Protocol:  auth_sha1_v4

 obfs:  tls1.2_ticket_auth

 Encryption Method:  chacha20

 


<!--more-->


Welcome to visit:https://www.91yun.org/archives/2079

 

 

卸载命令

 

./shadowsocksR.sh uninstall

 

 

升级命令

 

cd /usr/local/shadowsocks/shadowsocks

 

git pull

 

 

默认配置

 

加密：chacha20

 

协议：auth_sha1_v2

 

混淆参数：tls1 .2_ticket_auth

 

 

配置客户端

 

通用说明

 

每个系统上的客户端虽然操作界面和操作方法有些许差异，但是基本的配置参数和配置方法是一致的：

 

提供服务器位址、端口號、密碼、加密方式、協議 (SSR專用)、混淆參數 (SSR專用)等的设置功能，这些参数都来自于我们在自己服务器上的设置，只要填入这些设置就能开始番羽土啬了。

 

提供全局 (全部走代理)、自动 (按照PAC自动选择)、关闭 (全部不走代理)、自定义 (自定义哪些网站走代理)这四种模式。对于大多数普通用户来说，建议选择自动模式就能满足我们的需求了。

 

提供二维码分享服务器配置，扫描二维码快速添加服务器的功能。只需要借助二维码就能快速配置服务器。

 

SSR 服务器和客户端均

向下兼容SS

，所以推荐安装支持SSR的客户端。（

所以只安装ss的客户端即可

）

 

 

macOS

 

SSR客户端推荐：ShadowsocksX NG

 

SS客户端推荐：ShadowsocksX NG ( https://github.com/shadowsocks/ShadowsocksX-NG, 

注意：其默认的端口号是1086！

 )，ShadowsocksX(https://github.com/shadowsocks/shadowsocks-iOS/releases/download/2.6.3/ShadowsocksX-2.6.3.dmg)

 

 

Windows

 

SSR客户端推荐：ShadowsocksR

 

SS客户端推荐：Shadowsocks ( https://github.com/shadowsocks/shadowsocks-windows )

 

 

iOS

 

SSR客户端推荐：Shadowrocket ( https://itunes.apple.com/us/app/shadowrocket/id932747118，目前收费)，Wingy ( https://itunes.apple.com/us/app/id1178584911，目前免费)

 

SSR客户端备选：Surge ( https://itunes.apple.com/us/app/surge-web-developer-tool-proxy/id1040100637?mt=8 (中国区已下架)，官网-http://nssurge.com/，技术宅的神器)

 

 

Android

 

SSR客户端推荐：SSR

 

SS客户端推荐：影梭( https://play.google.com/store/apps/details?id=com.github.shadowsocks )

 

 

Linux 和其他系统

 

SSR客户端推荐：shadowsocksr-libev

 

SS客户端推荐：shadowsocks-libev ( https://github.com/shadowsocks/shadowsocks-libev )

 

\--------------

 

 

一键安装锐速破解版（来自https://www.91yun.org/archives/683）

 

 

为什么要安装「锐速」？

 

锐速是服务器端的TCP单边加速工具，可以提高连接服务器的速度。锐速原本是收费软体，目前已经停止开发，只能安装破解版。除了锐速外，还有免费的Finalspeed，但是Finalspeed需要额外的客户端支援才能运作（双边加速），而锐速不需要额外的客户端就能运作。

 

支持CentOS、Ubuntu、Debain 系统，不支持Openvz 架构的VPS

 

一键安装命令（如果内核完全匹配就会自动下载安装）

 

 

wget -N --no-check-certificate https://raw.githubusercontent.com/91yun/serverspeeder/master/serverspeeder-all.sh && bash serverspeeder-all.sh

 

 

一键卸载命令:

 

chattr -i /serverspeeder/etc/apx* && /serverspeeder/bin/serverSpeeder.sh uninstall -f

 

\---------

 

 

附录：

 

https://raw.githubusercontent.com/91yun/shadowsocks_install/master/shadowsocksR.sh的内容如下：

 

```
#! /bin/bash
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH
#=================================================================#
#   System Required:  CentOS 6,7, Debian, Ubuntu                  #
#   Description: One click Install ShadowsocksR Server            #
#   Author: 91yun <https://twitter.com/91yun>                     #
#   Thanks: @breakwa11 <https://twitter.com/breakwa11>            #
#   Thanks: @Teddysun <i@teddysun.com>                            #
#   Intro:  https://www.91yun.org/archives/2079                   #
#=================================================================#


#Current folder
cur_dir=`pwd`
# Get public IP address
IP=$(ip addr | egrep -o '[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}' | egrep -v "^192\.168|^172\.1[6-9]\.|^172\.2[0-9]\.|^172\.3[0-2]\.|^10\.|^127\.|^255\.|^0\." | head -n 1)
if [[ "$IP" = "" ]]; then
    IP=$(wget -qO- -t1 -T2 ipv4.icanhazip.com)
fi

# Make sure only root can run our script
function rootness(){
    if [[ $EUID -ne 0 ]]; then
       echo "Error:This script must be run as root!" 1>&2
       exit 1
    fi
}

# Check OS
function checkos(){
    if [ -f /etc/redhat-release ];then
        OS='CentOS'
    elif [ ! -z "`cat /etc/issue | grep bian`" ];then
        OS='Debian'
    elif [ ! -z "`cat /etc/issue | grep Ubuntu`" ];then
        OS='Ubuntu'
    else
        echo "Not support OS, Please reinstall OS and retry!"
        exit 1
    fi
}

# Get version
function getversion(){
    if [[ -s /etc/redhat-release ]];then
        grep -oE  "[0-9.]+" /etc/redhat-release
    else    
        grep -oE  "[0-9.]+" /etc/issue
    fi    
}

# CentOS version
function centosversion(){
    local code=$1
    local version="`getversion`"
    local main_ver=${version%%.*}
    if [ $main_ver == $code ];then
        return 0
    else
        return 1
    fi        
}

# Disable selinux
function disable_selinux(){
if [ -s /etc/selinux/config ] && grep 'SELINUX=enforcing' /etc/selinux/config; then
    sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
    setenforce 0
fi
}

# Pre-installation settings
function pre_install(){
    # Not support CentOS 5
    if centosversion 5; then
        echo "Not support CentOS 5, please change OS to CentOS 6+/Debian 7+/Ubuntu 12+ and retry."
        exit 1
    fi
    # Set ShadowsocksR config password
    echo "Please input password for ShadowsocksR:"
    read -p "(Default password: www.91yun.org):" shadowsockspwd
    [ -z "$shadowsockspwd" ] && shadowsockspwd="www.91yun.org"
    echo
    echo "---------------------------"
    echo "password = $shadowsockspwd"
    echo "---------------------------"
    echo
    # Set ShadowsocksR config port
    while true
    do
    echo -e "Please input port for ShadowsocksR [1-65535]:"
    read -p "(Default port: 8989):" shadowsocksport
    [ -z "$shadowsocksport" ] && shadowsocksport="8989"
    expr $shadowsocksport + 0 &>/dev/null
    if [ $? -eq 0 ]; then
        if [ $shadowsocksport -ge 1 ] && [ $shadowsocksport -le 65535 ]; then
            echo
            echo "---------------------------"
            echo "port = $shadowsocksport"
            echo "---------------------------"
            echo
            break
        else
            echo "Input error! Please input correct number."
        fi
    else
        echo "Input error! Please input correct number."
    fi
    done
    get_char(){
        SAVEDSTTY=`stty -g`
        stty -echo
        stty cbreak
        dd if=/dev/tty bs=1 count=1 2> /dev/null
        stty -raw
        stty echo
        stty $SAVEDSTTY
    }
    echo
    echo "Press any key to start...or Press Ctrl+C to cancel"
    char=`get_char`
    # Install necessary dependencies
    if [ "$OS" == 'CentOS' ]; then
        yum install -y wget unzip openssl-devel gcc swig python python-devel python-setuptools autoconf libtool libevent git ntpdate
        yum install -y m2crypto automake make curl curl-devel zlib-devel perl perl-devel cpio expat-devel gettext-devel
    else
        apt-get -y update
        apt-get -y install python python-dev python-pip python-m2crypto curl wget unzip gcc swig automake make perl cpio build-essential git ntpdate
    fi
    cd $cur_dir
}

# Download files
function download_files(){
    # Download libsodium file
    if ! wget --no-check-certificate -O libsodium-1.0.10.tar.gz https://github.com/jedisct1/libsodium/releases/download/1.0.10/libsodium-1.0.10.tar.gz; then
        echo "Failed to download libsodium file!"
        exit 1
    fi
    # Download ShadowsocksR file
    # if ! wget --no-check-certificate -O manyuser.zip https://github.com/breakwa11/shadowsocks/archive/manyuser.zip; then
        # echo "Failed to download ShadowsocksR file!"
        # exit 1
    # fi
    # Download ShadowsocksR chkconfig file
    if [ "$OS" == 'CentOS' ]; then
        if ! wget --no-check-certificate https://raw.githubusercontent.com/91yun/shadowsocks_install/master/shadowsocksR -O /etc/init.d/shadowsocks; then
            echo "Failed to download ShadowsocksR chkconfig file!"
            exit 1
        fi
    else
        if ! wget --no-check-certificate https://raw.githubusercontent.com/91yun/shadowsocks_install/master/shadowsocksR-debian -O /etc/init.d/shadowsocks; then
            echo "Failed to download ShadowsocksR chkconfig file!"
            exit 1
        fi
    fi
}

# firewall set
function firewall_set(){
    echo "firewall set start..."
    if centosversion 6; then
        /etc/init.d/iptables status > /dev/null 2>&1
        if [ $? -eq 0 ]; then
            iptables -L -n | grep '${shadowsocksport}' | grep 'ACCEPT' > /dev/null 2>&1
            if [ $? -ne 0 ]; then
                iptables -I INPUT -m state --state NEW -m tcp -p tcp --dport ${shadowsocksport} -j ACCEPT
                iptables -I INPUT -m state --state NEW -m udp -p udp --dport ${shadowsocksport} -j ACCEPT
                /etc/init.d/iptables save
                /etc/init.d/iptables restart
            else
                echo "port ${shadowsocksport} has been set up."
            fi
        else
            echo "WARNING: iptables looks like shutdown or not installed, please manually set it if necessary."
        fi
    elif centosversion 7; then
        systemctl status firewalld > /dev/null 2>&1
        if [ $? -eq 0 ];then
            firewall-cmd --permanent --zone=public --add-port=${shadowsocksport}/tcp
            firewall-cmd --permanent --zone=public --add-port=${shadowsocksport}/udp
            firewall-cmd --reload
        else
   /etc/init.d/iptables status > /dev/null 2>&1
   if [ $? -eq 0 ]; then
    iptables -L -n | grep '${shadowsocksport}' | grep 'ACCEPT' > /dev/null 2>&1
    if [ $? -ne 0 ]; then
     iptables -I INPUT -m state --state NEW -m tcp -p tcp --dport ${shadowsocksport} -j ACCEPT
     iptables -I INPUT -m state --state NEW -m udp -p udp --dport ${shadowsocksport} -j ACCEPT
     /etc/init.d/iptables save
     /etc/init.d/iptables restart
    else
     echo "port ${shadowsocksport} has been set up."
    fi
   else
    echo "WARNING: firewall like shutdown or not installed, please manually set it if necessary."
   fi  
        fi
    fi
    echo "firewall set completed..."
}

# Config ShadowsocksR
function config_shadowsocks(){
    cat > /etc/shadowsocks.json<<-EOF
{
    "server": "0.0.0.0",
    "server_ipv6": "::",
    "server_port": ${shadowsocksport},
    "local_address": "127.0.0.1",
    "local_port": 1081,
    "password": "${shadowsockspwd}",
    "timeout": 120,
    "udp_timeout": 60,
    "method": "chacha20",
    "protocol": "auth_sha1_v4_compatible",
    "protocol_param": "",
    "obfs": "tls1.2_ticket_auth_compatible",
    "obfs_param": "",
    "dns_ipv6": false,
    "connect_verbose_info": 1,
    "redirect": "",
    "fast_open": false,
    "workers": 1

}
EOF
}

# Install ShadowsocksR
function install_ss(){
    # Install libsodium
    tar zxf libsodium-1.0.10.tar.gz
    cd $cur_dir/libsodium-1.0.10
    ./configure && make && make install
    echo "/usr/local/lib" > /etc/ld.so.conf.d/local.conf
    ldconfig
    # Install ShadowsocksR
    cd $cur_dir
    # unzip -q manyuser.zip
    # mv shadowsocks-manyuser/shadowsocks /usr/local/
 git clone https://github.com/91yun/shadowsocksr-1.git /usr/local/shadowsocks
    if [ -f /usr/local/shadowsocks/server.py ]; then
        chmod +x /etc/init.d/shadowsocks
        # Add run on system start up
        if [ "$OS" == 'CentOS' ]; then
            chkconfig --add shadowsocks
            chkconfig shadowsocks on
        else
            update-rc.d -f shadowsocks defaults
        fi
        # Run ShadowsocksR in the background
        /etc/init.d/shadowsocks start
        clear
        echo
        echo "Congratulations, ShadowsocksR install completed!"
        echo -e "Server IP: \033[41;37m ${IP} \033[0m"
        echo -e "Server Port: \033[41;37m ${shadowsocksport} \033[0m"
        echo -e "Password: \033[41;37m ${shadowsockspwd} \033[0m"
        echo -e "Protocol: \033[41;37m auth_sha1_v4 \033[0m"
        echo -e "obfs: \033[41;37m tls1.2_ticket_auth \033[0m"
        echo -e "Encryption Method: \033[41;37m chacha20 \033[0m"
        echo "Welcome to visit:https://www.91yun.org/archives/2079"
        echo "If you want to change protocol & obfs, reference URL:"
        echo "https://github.com/breakwa11/shadowsocks-rss/wiki/Server-Setup"
        echo
        echo "Enjoy it!"
        echo
    else
        echo "Shadowsocks install failed!"
        install_cleanup
        exit 1
    fi
}


# Install cleanup
function install_cleanup(){
    cd $cur_dir
    rm -f manyuser.zip
    rm -rf shadowsocks-manyuser
    rm -f libsodium-1.0.10.tar.gz
    rm -rf libsodium-1.0.10
}


# Uninstall ShadowsocksR
function uninstall_shadowsocks(){
    printf "Are you sure uninstall ShadowsocksR? (y/n) "
    printf "\n"
    read -p "(Default: n):" answer
    if [ -z $answer ]; then
        answer="n"
    fi
    if [ "$answer" = "y" ]; then
        /etc/init.d/shadowsocks status > /dev/null 2>&1
        if [ $? -eq 0 ]; then
            /etc/init.d/shadowsocks stop
        fi
        checkos
        if [ "$OS" == 'CentOS' ]; then
            chkconfig --del shadowsocks
        else
            update-rc.d -f shadowsocks remove
        fi
        rm -f /etc/shadowsocks.json
        rm -f /etc/init.d/shadowsocks
        rm -rf /usr/local/shadowsocks
        echo "ShadowsocksR uninstall success!"
    else
        echo "uninstall cancelled, Nothing to do"
    fi
}


# Install ShadowsocksR
function install_shadowsocks(){
    checkos
    rootness
    disable_selinux
    pre_install
    download_files
    config_shadowsocks
    install_ss
    if [ "$OS" == 'CentOS' ]; then
        firewall_set > /dev/null 2>&1
    fi
 #check_datetime
    install_cleanup
 
}

# Initialization step
action=$1
[ -z $1 ] && action=install
case "$action" in
install)
    install_shadowsocks
    ;;
uninstall)
    uninstall_shadowsocks
    ;;
*)
    echo "Arguments error! [${action} ]"
    echo "Usage: `basename $0` {install|uninstall}"
    ;;
esac
```

```
-------
```

```

```

# ShadowsocksR一键安装脚本

本脚本适用环境： 系统支持：CentOS，Debian，Ubuntu 内存要求：≥128M 日期：2017 年 07 月 27 日

关于本脚本： 一键安装 ShadowsocksR 服务端。 请下载与之配套的客户端程序来连接。 （以下客户端只有 [Windows 客户端](https://github.com/shadowsocksr/shadowsocksr-csharp/releases)和 [Python 版客户端](https://github.com/breakwa11/shadowsocks-rss/wiki/Python-client)可以使用 SSR 新特性，其他原版客户端只能以兼容的方式连接 SSR 服务器）

默认配置： 服务器端口：自己设定（如不设定，默认为 8989） 密码：自己设定（如不设定，默认为 teddysun.com） 加密方式：自己设定（如不设定，默认为 aes-256-cfb） 协议（Protocol）：自己设定（如不设定，默认为 origin） 混淆（obfs）：自己设定（如不设定，默认为 plain）

客户端下载： [Windows](https://github.com/shadowsocksr/shadowsocksr-csharp/releases) / [OS X](https://github.com/shadowsocks/shadowsocks-iOS/wiki/Shadowsocks-for-OSX-Help) [Linux](https://github.com/librehat/shadowsocks-qt5) [Android](https://github.com/shadowsocks/shadowsocks-android) / [iOS](https://github.com/shadowsocks/shadowsocks-iOS/wiki/Help) [OpenWRT](https://github.com/shadowsocks/openwrt-shadowsocks)

使用方法： 使用root用户登录，运行以下命令：

```
wget --no-check-certificate https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocksR.sh
chmod +x shadowsocksR.sh
./shadowsocksR.sh 2>&1 | tee shadowsocksR.log
```

安装完成后，脚本提示如下：

```
Congratulations, ShadowsocksR server install completed!
Your Server IP        :your_server_ip
Your Server Port      :your_server_port
Your Password         :your_password
Your Protocol         :your_protocol
Your obfs             :your_obfs
Your Encryption Method:your_encryption_method

Welcome to visit:https://shadowsocks.be/9.html
Enjoy it!
```

卸载方法： 使用 root 用户登录，运行以下命令：

```
./shadowsocksR.sh uninstall
```

安装完成后即已后台启动 ShadowsocksR ，运行：

```
/etc/init.d/shadowsocks status
```

可以查看 ShadowsocksR 进程是否已经启动。 本脚本安装完成后，已将 ShadowsocksR 自动加入开机自启动。

使用命令： 启动：/etc/init.d/shadowsocks start 停止：/etc/init.d/shadowsocks stop 重启：/etc/init.d/shadowsocks restart 状态：/etc/init.d/shadowsocks status

配置文件路径：/etc/shadowsocks.json 日志文件路径：/var/log/shadowsocks.log 代码安装目录：/usr/local/shadowsocks

多用户配置示例：

```
{
"server":"0.0.0.0",
"server_ipv6": "[::]",
"local_address":"127.0.0.1",
"local_port":1080,
"port_password":{
    "8989":"password1",
    "8990":"password2",
    "8991":"password3"
},
"timeout":300,
"method":"aes-256-cfb",
"protocol": "origin",
"protocol_param": "",
"obfs": "plain",
"obfs_param": "",
"redirect": "",
"dns_ipv6": false,
"fast_open": false,
"workers": 1
}
```

如果你想修改配置文件，请参考： <https://github.com/breakwa11/shadowsocks-rss/wiki/Server-Setup><https://github.com/breakwa11/shadowsocks-rss/blob/master/ssr.md> <https://github.com/breakwa11/shadowsocks-rss/wiki/config.json>

更新日志： 2017 年 07 月 27 日： 1、新增：可选协议（protocol）auth_chain_b 。使用该协议需更新到最新版（4.7.0）[ShadowsocksR 版客户端](http://dl.teddysun.com/files/ShadowsocksR-4.7.0-win.7z)； 2、修改：更新 ShadowsocksR 源码下载地址。

2017 年 07 月 22 日： 1、新增：安装时可选 13 种加密方式的其中之一（none 是不加密）。如下所示：

```
none
aes-256-cfb
aes-192-cfb
aes-128-cfb
aes-256-cfb8
aes-192-cfb8
aes-128-cfb8
aes-256-ctr
aes-192-ctr
aes-128-ctr
chacha20-ietf
chacha20
rc4-md5
rc4-md5-6
```

2、新增：安装时可选 7 种协议（protocol）的其中之一。如下所示：

```
origin
verify_deflate
auth_sha1_v4
auth_sha1_v4_compatible
auth_aes128_md5
auth_aes128_sha1
auth_chain_a
auth_chain_b
```

3、新增：安装时可选 9 种混淆（obfs）的其中之一。如下所示：

```
plain
http_simple
http_simple_compatible
http_post
http_post_compatible
tls1.2_ticket_auth
tls1.2_ticket_auth_compatible
tls1.2_ticket_fastauth
tls1.2_ticket_fastauth_compatible
```

2016 年 08 月 13 日： 1、新增多用户配置示例。注意：如果你新增了端口，也要将该端口从防火墙（iptables 或 firewalld）中打开。

2016 年 05 月 12 日： 1、新增在 CentOS 下的防火墙规则设置。

from https://shadowsocks.be/9.html

http://vpn.ximcx.cn/SSR/SSR ，这个只是复制品）