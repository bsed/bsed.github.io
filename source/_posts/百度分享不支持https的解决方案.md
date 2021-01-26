---
title: "百度分享不支持https的解决方案"
categories: [ "JS" ]
tags: [ "javascript","sharejs","https" ]
draft: false
slug: "baidu-shares-do-not-support-https-solutions"
date: "2016-06-22 12:50:00"
---

站点自从开启 https 之后 ，百度分享就不能用了！但是又寻找不到类似百度分享的替代品。。

怎么办呢？要如何解决 百度分享不支持https的问题呢，

跟着博主动动手，让你百度分享仍然能在https下使用 ~


<!--more-->


## 伸手党

先上伸手党的解决方案~

博主修改好的分享代码(下面两个链接下载其中一个的即可)

[http://vdisk.weibo.com/lc/2wtonQtT2CvFl8909yp](http://vdisk.weibo.com/lc/2wtonQtT2CvFl8909yp) 密码：`Q0A1`
备用地址： [http://pan.baidu.com/s/1i4c1hnn](http://pan.baidu.com/s/1i4c1hnn) 密码：`0kjw`
static 解压后丢到站点根目录下即可。

然后对应的百度分享代码中，把 `http://bdimg.share.baidu.com/`改为 `/`

```javascript
.src='http://bdimg.share.baidu.com/static/api/js/share.js?v=89860593.js?cdnversion='+~(-new Date()/36e5)];</script>
```
改为
```javascript
.src='/static/api/js/share.js?v=89860593.js?cdnversion='+~(-new Date()/36e5)];</script>
```
实际上
```javascript
.src='http://bdimg.share.baidu.com/static/api/js/share.js?v=89860593.js?cdnversion='+~(-new Date()/36e5)];</script>
```
改为
```javascript
.src='/static/api/js/share.js?v=89860593.js?cdnversion='+~(-new Date()/36e5)];</script>
```

## 自己动手，丰衣足食

嗯，直接这么丢根目录下，万一坏心眼的人想使坏呢？给你来个XSS的就完蛋了。

当然，博主绝不是这种人。所以博主把修改过程放出来，授人以鱼不如授人以渔，也让安全意识高的童鞋自己动手，放宽心^ ^

 

## 获取百度分享的文件
你可以直接用 `chrome – F12 – source` 查看有什么文件，然后保存（注意微信的需要先点一下，对应的 css 才会出来）

![baidu-share-download.png][1]

也可以直接看博主分享的文件，对应着下载。

把域名改成`http://bdimg.share.baidu.com/`加上对应的路径即可

 

修改一些文件
直接这么丢上去是会有问题的，你地址栏绿色的小锁会消失 T^T

就是说你虽然是Https的站点，但请求了http的资源，这种混合模式是不好滴

**修复方法**：

static\api\js\share.js

`domain: {staticUrl: "http://bdimg.share.baidu.com/"}` 修改为 `domain: {staticUrl: "/"}`
`nsClick: "http://nsclick.baidu.com/v.gif"  修改为  nsClick: "/",

static\api\js\trans\logger.js
把里面的内容都删掉，但保留这个空文件
貌似就是这两个步骤，博主应该没漏掉

## 修改印象笔记图标
博主用的是主题1（明显比主题0好看啊，主题0还没印象笔记图标），

但是由于下面那个更多用的是主题0的图片，会造成加载两个图片，让我微微不爽（同样的没有印象笔记图标！）

于是修改如下：

static\api\css\share_popup.css
最后加上 `.popup_evernotecn{background-position:4px -3190px;}`
static\api\css\share_style0_16.css
最后加上 `.bds_evernotecn{background-position:0 -3195px}`
把 icons_1_16.png 重命名为 icons_0_16.png
可选：用tinypng 把图片压缩下，博主现在这个图片就7.81KB
 
## 写在最后

OK，快去试试效果吧~

如果有不清楚的地方，欢迎留言。

你也可以对着我修改的结果查看~

原文：[https://www.hrwhisper.me/baidu-share-not-support-https-solution/](https://www.hrwhisper.me/baidu-share-not-support-https-solution/)


  [1]: https://imgs.gnux.cn/usr/uploads/2016/06/3505987201.png