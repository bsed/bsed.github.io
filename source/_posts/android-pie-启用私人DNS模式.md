---
title: "android pie 启用私人DNS模式"
categories: [ "Linux" ]
tags: [ "dns","DoT" ]
draft: false
slug: "android-pie-enables-private-dns-mode"
date: "2019-03-10 07:42:00"
---

打开 [设置] → 网络和互联网 → 高级 → 私人 DNS → 私人 DNS 提供的主机名 → 输入上述提供商提供的主机名 → 保存


<!--more-->


```bash
DNS over TLS 提供商
Google: dns.google

Cloudflare: 1dot1dot1dot1.cloudflare-dns.com

Quad9: dns.quad9.net

CleanBrowsing: security-filter-dns.cleanbrowsing.org

红鱼 DNS: dns.rubyfish.cn
```

保存以后，如果私人 DNS 下方显示主机名代表配置成功，如果显示“无法连接”，参考按照以下方法排查：

关闭 VPN 连接，有 VPN 的时候是不能用的。
如果你的 WiFi 或 移动网络图标旁边有 × 标志，启用系统的开发者模式，使用 ADB 来消除此标志。参考：https://www.evil42.com/index.php/archives/17/
配置完成后就可以使用了。

如果你使用的是 Cloudflare 提供的私人 DNS，可以访问 [https://1.1.1.1/help](https://1.1.1.1/help)，验证是否配置成功。