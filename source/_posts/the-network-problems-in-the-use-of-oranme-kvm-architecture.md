---
title: OranMe KVM VPS 架构使用过程出现的网络问题
date: 2018-11-07 15:25:00
updated: 2018-12-27 08:48:55
tags: 
- css
- 居中
categories: 
- css

---
这家vps公司客服比较给力，有问题及时帮助解决。
![98591-vd0hn3d8zzm.png](https://imgs.gnux.cn/usr/uploads/2018/11/1798129308.png)
![41381-6zimq10wo2q.png](https://imgs.gnux.cn/usr/uploads/2018/11/3147619896.png)
![24315-66tdsqshbga.png](https://imgs.gnux.cn/usr/uploads/2018/11/1177959387.png)

顺便google了下 debian ifup 的用法，记录一下。
## ebian IP地址配置
vi /etc/network/interface


<!--more-->


## 配置网卡eth0的IP地址
auto eth0 #表示网卡随系统自动请
iface eth0 inet static #表示网卡为静态ip地址
address 192.168.2.147 #表示设置ip地址
netmask 255.255.255.0 #表示子网掩码
gateway 192.168.2.1 #表示网关，不为了上网，可以不填，只用于局域网通信

`#号`后的为注释，注意删除。

指定网卡重启
ifdown eth0
ifup eth0

网络重启
/etc/init.d/networking restart
service networking restart

设置双网卡
需求：有两个网卡，一个是接外网（无限网卡wlan0），一个是接内网(eth0)

注意：
当有两个网卡，一个是无线网卡，一个是有线网卡，一定要将无线网卡的详细配置写入，例如设置静态地址，SSID，密码等等
![30745-rp9dg0dn2bn.png](https://imgs.gnux.cn/usr/uploads/2018/11/2353351053.png)

注意：
auto：开机启动，设置了2个网卡都开机启动
ip地址获取方式，static是静态IP地址，dhcp是DHCP获取地址。
设置两个网卡同时启动的时候，需要将一个网卡的网管置空，否则需要手动修改路由，删除其中的一条默认路由。

auto与allow-hotplug的区别

/etc/network/interfaces文件中一般用auto或者allow-hotplug来定义接口的启动行为。

auto

语法：
auto <interface_name>
含义：
在系统启动的时候启动网络接口,无论网络接口有无连接(插入网线),如果该接口配置了DHCP,则无论有无网线,系统都会去执行DHCP,如果没有插入网线,则等该接口超时后才会继续。

allow-hotplug

语法:
allow-hotplug <interface_name>

含义：
只有当内核从该接口检测到热插拔事件后才启动该接口。如果系统开机时该接口没有插入网线,则系统不会启动该接口,系统启动后,如果插入网线,系统会自动启动该接口。也就是将网络接口设置为热插拔模式。

手动重新启动网络

一般修改了网络配置文件后,会用以下命令重新启动网络
# /etc/init.d/networking restart
但从squeeze开始,此命令会有如下提示:


Running /etc/init.d/networking restart is deprecated because it may not enable again some interfaces … (warning).
Reconfiguring network interfaces…done.

如果设置接口为auto,虽然会有如此提示,但接口仍然会正确的启动。
如果接口设置为allow-hotplug则没有这么走运了,网络接口不会正确启动。这种情况下必须使用如下命令启动网络接口:
#ifup <interface_name>
而命令
#ifconfig <interface_name> up
也无法正确启动接口

所以allow-hotplug设置的接口最好如下方式重新启动网络接口,当然auto方式的接口也没问题:

#ifdown <interface_name> && ifup <interface_name>

特别是在ssh登录远程主机的情况下,一定要像上面这样在一条命令里执行ifdown和ifup,否则,如果先执行ifdown,则再也没有机会执行ifup了。

参考：[https://docs.cumulusnetworks.com/display/DOCS/Interface+Configuration+and+Management](https://docs.cumulusnetworks.com/display/DOCS/Interface+Configuration+and+Management)