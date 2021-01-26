---
title: "SS及SSR该怎么配置协议混淆才是最佳"
categories: [ "Linux" ]
tags: [ "ssr" ]
draft: false
slug: "ss-and-ssr-how-to-configure-protocol-confusion-is-the-best"
date: "2018-10-23 04:19:00"
---

首先理解安全：
第一个是数据安全，SS及SSR只要不使用弱加密，那么有生之年你通过SS或SSR传输的数据都是安全的。

第二个是我使用SS及SSR会不会被喝TEA，以目前的环境而言，只要你不是用于非法用途，没人会在意你利用SS或SSR多看了会汤博。举栗：开着SS(R)看了某gui的视频，突然想知道这个人的资料，随手打开百度，顺便进入各种贴吧各种Q群大谈特谈此人某事，拜托，不请你喝TEA请谁？不知道为什么？黑人问号？那你不配用SS(R)！

SS或SSR如何解决以上可能潜在的安全问题，或者说怎么拯救大家脆弱的安全感？
两个不同的方向，虽然后来也趋同。SSR在于希望通过添加协议混淆伪装成正常流量达到骗过所谓“主动嗅探”。而SS是以数据加密的方向解决这个问题，如AEAD加密方式，只是后来也希望通过obfs混淆减少流量特征。

但是，请注意，以上的解决方法是以程序员的代码逻辑给出的最佳方案，但，是否真的会骗过GFW?或者说，GFW真的需要嗅探到这些流量特征才知道你在FQ？我认为，这是个未知数。


<!--more-->


啰嗦至此！

那么，我们先假定SS及SSR是目前FQ的最佳方式。呃…
正题：

目前SS端最推荐的加密以及obfs混淆如下：
推荐加密方式：aes-256-gcm 、chacha20-ietf-poly1305、aes-128-gcm、aes-192-gcm （排名分先后）

推荐的混淆obfs：首选http、次选tls

注：加密方式推荐是因为AEAD本身有新的特性，另外主推荐aes-256-gcm是因为这个加密实测多数主流设备下都可以通过硬件加解密，减少系统性能资源的依赖。

混淆方式理论上tls是最佳，但目前以联通上网记录最直观的结果来看，使用tls混淆会使混淆域名“无效”直接显示代理服务器IP，而http可以正常显示混淆域名，至少看上去达到了伪装的效果，故，目前而言，推荐使用http混淆。

综上所述：

SS最佳推荐加密方式：aes-256-gcm，最佳obfs：http 目前手机端、第三方路由固件多数已支持，建议使用以上推荐，PC客户端有待更新，相信不会等太久。

SSR端最推荐的加密、协议以及obfs混淆如下：
推荐加密方式：chacha20-ietf、aes-128-ctr及其它

推荐的最佳协议：auth_sha1_v4_compatible及其它

推荐的最佳混淆：tls1.2_ticket_auth_compatible及其它

注：我不是偷懒，SSR可选项多，自定义多，这是优点也算是缺点，按开发者所表达的，最好的是将来她要发布的，但后来就没有了后来…

目前网络综合环境而言，以上推荐就已经是最佳推荐，最好的组合。那为什么不是最新的auth_chain_a或b? 答案很简单，这2个协议在服务端上并没有上面推荐的协议稳定，并且更耗系统资源，在客户端面对不同网络环境下也一样会出现不稳定的情况，这也就是为什么有的人用了觉得比以前的协议混淆都好，而有的人用了反而会更慢。

另：协议跟混淆的“_compatible”是什么鬼？

带_compatible表示兼容SS原版协议，也就是服务端不用修改协议跟混淆，只要在客户端将协议改为origin ，将混淆改为plain，那么它就成了SS。这个在利用SSR玩外服游戏时极为重要，因为增加协议与混淆会增加延迟及一些莫名其妙的问题，所有玩外服游戏时建议按以上修改：协议改为origin，混淆改为plain。

注意！SS的obfs混淆在玩外服游戏时也建议设置为none！！！
SS及SSR推荐混淆域名参考:
国内域名混淆：（平常访问以下站点那个多就用那个，或定期更换）

优酷：
r1.ykimg.com

static.youku.com

B站：
tx.acgvideo.com

static.hdslb.com

i3.hdslb.com

淘宝：
img.alicdn.com

item.taobao.com

国外混淆：（因为节点均为非大陆ip，所以合理性来说用国外混淆才是最好的伪装）
tse3.mm.bing.net

tse2.mm.bing.net

tse1.mm.bing.net

cloudfront.com

cloudflare.com

itunes.apple.com

www.icloud.com

ajax.microsoft.com

apps.bdimg.com

www.bing.com

不要问到底用那个好，国内好还是国外好，这些问题没答案，选择其中1个就可以。
在客户端使用，填写在混淆参数项里。注意：仅填一个就好！

[http://blog.yukihd.com/2018/01/22/%E5%BD%93%E5%89%8D%E7%BD%91%E7%BB%9C%E7%8E%AF%E5%A2%83%EF%BC%8Css%E5%8F%8Assr%E8%AF%A5%E6%80%8E%E4%B9%88%E9%85%8D%E7%BD%AE%E5%8D%8F%E8%AE%AE%E6%B7%B7%E6%B7%86%E6%89%8D%E6%98%AF%E6%9C%80%E4%BD%B3/](http://blog.yukihd.com/2018/01/22/%E5%BD%93%E5%89%8D%E7%BD%91%E7%BB%9C%E7%8E%AF%E5%A2%83%EF%BC%8Css%E5%8F%8Assr%E8%AF%A5%E6%80%8E%E4%B9%88%E9%85%8D%E7%BD%AE%E5%8D%8F%E8%AE%AE%E6%B7%B7%E6%B7%86%E6%89%8D%E6%98%AF%E6%9C%80%E4%BD%B3/)