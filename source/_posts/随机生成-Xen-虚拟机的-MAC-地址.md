---
title: "随机生成 Xen 虚拟机的 MAC 地址"
categories: [ "Linux" ]
tags: [ "xen","mac" ]
draft: false
slug: "linux-xen-mac-address"
date: "2014-11-05 17:29:00"
---

如果不在创建 Xen 虚拟机（domU）的时候指定 MAC 地址的话，Xen 就会随机生成一个 MAC 地址给虚拟机，这样管理员就没办法知道虚拟机的 MAC 地址了，不利于以后带宽的统计和虚拟机的管理，所以最好就在 Xen 虚拟机配置文件中直接指明虚拟机的 MAC 地址，比如：


<!--more-->


    # vi /etc/xen/vpsuser1
    vif = [ "mac=00:16:3e:0c:11:53,ip=172.16.16.200,bridge=xenbr0" ]

IEEE OUI 为 Xen domU 保留了一段 MAC 地址，前3段是 “00-16-3e”，后3段是随机的，其中第1个随机段的第1个 bit 是0（IEEE OUI 给 VMware 保留的前3段地址是 “00-0c-29”，后3段随机）。我们在为用户分配 MAC 地址时候不想有规律的分配（虽然也不会有什么安全问题），所以写了一个简单的 Python 脚本用来随机生成符合 Xen 虚拟机标准的 MAC 地址：

    #!/usr/bin/python 
    # generates a MAC address for Xen domU
    # http://www.vpsee.com
    #
    
    import random
    
    mac = [ 0x00, 0x16, 0x3e, random.randint(0x00, 0x7f), 
    random.randint(0x00, 0xff), random.randint(0x00, 0xff) ]
    s = []
    for item in mac:
            s.append(str("%02x" % item))
    print ':'.join(s)