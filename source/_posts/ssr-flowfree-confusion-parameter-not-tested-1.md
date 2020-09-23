---
title: ssr 免流混淆参数 【未测试】
date: 2019-08-20 13:14:00
updated: 2019-08-20 13:14:35
tags: 
- ES2019
categories: 
- js

---
**使用方法**
举个例子：我使用的是大鱼卡，而大鱼卡对高德地图是免流的，那么我就可以使用高德地图的混淆参数，把客户端的混淆参数改成 `mps.amap.com#Connection: Keep-AlivenAccept-Encoding: gzipnContent-Length: 680`，代理模式设置为全局，安卓客户端的代理模式在路由那里 (默认全局，无需修改)，IOS 小火箭的全局路由改成代理模式就可以了，理论支持电信联通所有互联网套餐卡！

## 联通普卡混淆

全国联通沃商店混淆参数1:game.hxll.wostore.cn 


<!--more-->


全国联通沃商店混淆参数2:music.hxll.wostore.cn 

沃阅读混淆参数:partner.iread.wo.com.cn 

全国联通沃商店高级混淆参数1:game.hxll.wostore.cn#Connection: Keep-Alive\nAccept-Encoding: gzip 

全国联通沃商店高级混淆参数2:music.hxll.wostore.cn#Connection: Keep-Alive\nAccept-Encoding: gzip 

沃阅读高级混淆参数:partner.iread.wo.com.cn#Connection: Keep-Alive\nAccept-Encoding: gzip 

(理论全国可用，吉林北京河南山东安徽江苏福建江西浙江广西贵州湖南湖北深圳已免。） 

全国联通沃音乐：box.10155.com 

普通免流混淆参数

QQ音乐混淆参数:dl.stream.qqmusic.com 

梦想e卡混淆参数1:imhdfs.icbc.com.cn 

梦想e卡混淆参数2(福建已免):v.icbc.com.cn 

爱奇艺混淆参数:data.video.qiyi.com 

优酷混淆参数:vali-dns.cp31.ott.cibntv.net 

高德地图混淆参数:mps.amap.com 

央视影音混淆参数:asp.cntv.myalicdn.com 

百度贴吧混淆参数:static.tieba.baidu.com 

微信混淆参数(遵义广州已免):short.weixin.qq.com 

虾米音乐混淆参数:pic.xiami.net 

天翼视讯混淆参数:vod3.nty.tv189.cn 

钉钉混淆参数：tms.dingtalk.com 

微博混淆参数：tobe.vip.weibo.com 

微博混淆参数：new.vip.weibo.cn 

微博混淆参数：simg.s.weibo.com 

优酷混淆参数：push.m.youku.com 

优酷混淆参数：api.mobile.youku.com 

书旗混淆参数：spend1.shuqireader.com 

书旗混淆参数：c1.shuqireader.com 

优酷混淆参数：www.youku.com 

高级免流混淆参数

优酷高级混淆参数:vali-dns.cp31.ott.cibntv.net#Range:bytes=25165824-32586598\nAccept: */* 

爱看4G高级混淆参数:ltevod.tv189.cn#Connection: Keep-Alive\nAccept-Encoding: gzip 

沃音乐高级混淆参数:woif.10155.com#Accept-Encoding: gzip 

微信高级混淆参数(深圳已免):szminorshort.weixin.qq.com#Upgrade: mmtls\nAccept: */*\nConnection: close\nContent-Length: 533\nContent-Type: application/octet-stream 

淘宝高级混淆参数:adashbc.m.taobao.com#Accept-Encoding: gzip 

央视影音高级混淆参数:asp.cntv.myalicdn.com#Icy-MetaData: 1 

抖音高级混淆参数:dm.toutiao.com#Connection: Keep-Alive\nAccept-Encoding: gzip 

百度贴吧高级混淆参数:tbcdn.hiphotos.baidu.com#needginfo: 1/nConnection: Keep-Alive/nUser-Agent: bdtb for Android 9.0.8.0 

爱奇艺高级混淆参数:data.video.qiyi.com#Accept: */* 

美团高级混淆参数:apimeishi.meituan.com#Connection: Keep-Alive 

高德地图高级混淆参数:mps.amap.com#Connection: Keep-Alive\nAccept-Encoding: gzip\nContent-Length: 680 

## 移动 

www.10086.cn 

mm.10086.cn 

抖音卡头条卡定向 有定向包可用dm.toutiao.com 

抖音卡头条卡定向 有定向包可用v9-dy.ixigua.com 

## 电信 

天翼视讯 h5.nty.tv189.com 

天翼视讯 vod3.nty.tv189.cn 

爱玩端口任意 open.4g.play.cn 

爱看端口任意 ltetp.tv189.com 

湖北电信：hb.10000shequ.com:8081 

电信天翼：dy3.nty.tv189.cn 

电信天翼：ltewap.tv189.com 

电信天翼：tp.nty.tv189.com 

电信天翼：ltewap.tv189.com/ik4g/v/C40605803.html?appid=115020310073&token=baebaf0fda892726032db7e8f2f7e0ee&devid=000001&version=5.3.13.14ctch1&channelid=01832020\r\n 

电信爱听：dl.music.189.cn/res/V/1388/mp3/33/58/94/1388335894003000.mp3?mb=15380197563&fs=10104163&s=800&n=&id=63696337&M=online&sid=240387514 

电信爱玩：open.4g.play.cn/api/v2/egame/log.json&access_token=dc15d6902663913b10a6ff56ae4e0c44&imsi=460022443530655&vc=145&app_key=8888015&channel_id=20310025 

电信大王卡(可欠费使用)：lt.hn.189.cn:1082 

阿里鱼卡：mps.amap.com#Connection: Keep-Alive\nAccept-Encoding: gzip\nContent-Length: 680 


## 联通 

山东联通：m.t.17186.cn
联通 114 圣子全国：114.255.201.163 (重庆、河南、福建、湖南等地)
哔哩哔哩：i1.hdslb.com
哔哩哔哩：i0.hdslb.com
浙江联通 wo+：w.zj165.com
联通 wotv 全国：wotv.17wo.cn（浙江、安徽、江苏）
河北：hb.10000shequ.com
湖北 (优先 wap 接入点不免 net)：box.10155.com
沃音乐：box.10155.com (四川、湖北、重庆、河北等)
沃阅读：partner.iread.wo.com.cn (北京、河南等)
工商 e 卡：v.icbc.com.cn

m.icbc.com.cn 

m.mall.icbc.com.cn 

elife.icbc.com.cn 

act.icbc.com.cn 

hit.icbc.com.cn 

pv.mall.icbc.com.cn 

mybank.icbc.com.cn 

腾讯大王卡：szminorshort.weixin.qq.com#Upgrade: mmtlsnAccept: /nConnection: closenContent-Length: 533nContent-Type: application/octet-stream
腾讯大王卡：tx.flv.huya.com
腾讯大王卡：short.weixin.qq.com
腾讯大王卡：szextshort.weixin.qq.com
short.weixin.qq.com
百度圣卡：data.video.qiyi.com
阿里宝卡：vali-dns.cp31.ott.cibntv.net
阿里宝卡微博卡：gw.alicdn.com
优酷：api.mobile.youku.com
米粉 api：api.ad.xiaomi.com
小米应用商店：data.mistat.xiaomi.com
小米直播：zbupic.zb.mi.com
小米充值：f100.g.mi.com
小米商城：mi.com

联通卡注意：联通卡不管什么套餐请优先用沃商店混淆！例如使用的是联通大王卡，大王卡对微信是免流的，所以使用微信混淆参数，通过联通掌上营业厅查询免费流量使用总量增加就是免流成功！而大王卡当月超过 40G 是不计算大王卡免费应用的！所以 40G 后使用全国混淆即可继续免流！或者不用微信直接用全国混淆也行！，要免流代理模式必须全局！接入点优先 net！

转载自: [https://la4ji.blogspot.com/2019/07/ssr.html](https://la4ji.blogspot.com/2019/07/ssr.html)