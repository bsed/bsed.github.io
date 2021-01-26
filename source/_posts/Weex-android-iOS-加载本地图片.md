---
title: "Weex android iOS 加载本地图片"
categories: [ "Android" ]
tags: [ "weex" ]
draft: false
slug: "weex-android-ios-loads-local-pictures"
date: "2017-09-04 08:48:00"
---

## H5 页面的图片怎么兼容

```
http://localhost:1337/src/images/${img_name}

```

## Android 的实现方法

图片存储的目录


<!--more-->


res/drawable-hdpi

下面会提到，为何要存储到这里

引用方法

```
<img src="local:///test" style="width: 300; height: 300;">

```

不需要加图片的文件名后缀

注意：是三个斜杠，不是两个，否则会报错

> E/weex: Local src format is invalid.

如果你是在google play上上架。建议mdpi,hdpi,xhdpi,xxhdpi每一种都放一套（ldpi就算了，基本看不到），因为google play会根据不同的手机density来打不同的apk包（举个栗子，如果是hdpi的机器，下载下来的就只有hdpi的资源） 如果是在国内的市场话。建议只放一套（h或者xhpdi），因为国内市场是没有上面那种机制的，放多套资源会导致安装包变得很大。 此外： Android在没有找到相应dpi的图片时，会用其他density的图片进行缩放处理。因此会损失一些性能和内存（作为缩放的buffer使用）

参考 [Android 开发中 drawable 有必要放多套分辨率的图片资源吗？](https://www.zhihu.com/question/28850798)

## iOS 的实现方法

图片存储目录

main bundle

引用方法

```
<img src="local://test.png" style="width: 300; height: 300;">

```

我这里直接报 domain error， 而不是 no resource found 说明并没有走最新的 SDK 代码流程。

最终在官方文档 [Path (英) | Weex](http://weex-project.io/cn/references/path.html) 的相对路径部分找到了解决方法。

即使用相对路径即可！

xcode 最傻逼的地方是，需要手动将图片拖动到项目的 resource 目录。 需要自动添加的方案！！！

先手动在 resource 目录新建一个 images 目录，然后，不断往这个目录里新增图片文件即可。

## 图片的同步

npm run copy:xx 解决

package.json

```
  "scripts": {
    "build": "webpack",
    "dev": "npm run build && webpack --watch",
    "copy:android": "mkdir -p platforms/android/app/src/main/assets/dist/; cp dist/index.weex.js platforms/android/app/src/main/assets/dist/index.js; cp src/images/*.* platforms/android/app/src/main/res/drawable-hdpi/",
    "copy:ios": "mkdir -p platforms/ios/bundlejs/ && cp dist/index.weex.js platforms/ios/bundlejs/index.js && cp src/images/*.* platforms/ios/images/",
    "copy": "npm run copy:android && npm run copy:ios",
    "serve": "serve -p 1337",
    "test": "echo \"Error: no test specified\" && exit 1"
  },

```

## 实现代码

src/mixins/index.js

```
        // 获取图片在三端上不同的路径
	// e.g. 图片文件名是 test.jpg, 转换得到的图片地址为
	// - H5      : http: //localhost:1337/src/images/test.jpg
	// - Android : local:///test
	// - iOS     : ../images/test.jpg
	get_img_path(img_name) {
		let platform = weex.config.env.platform
		let img_path = ''

		if (platform == 'Web') {
			img_path = `http://localhost:1337/src/images/${img_name}`
		} else if (platform == 'android') {
			// android 不需要后缀
			img_name = img_name.substr(0, img_name.lastIndexOf('.'));
			img_path = `local:///${img_name}`
		} else {
			img_path = `../images/${img_name}`
		}

		return img_path
	}

```

使用方法

```
<image style="width: 120px; height: 120px;"  :src="get_img_path('test.png')"></image>

```

## 参考

- [weex中怎么加载本地图片](https://segmentfault.com/q/1010000009290128)
- [Learn Weex The Hard Way (如何在 Weex 中使用图标) | 三关茅庐](http://kevin.doyeden.com/2017/03/08/learn-weex-the-hard-way-3/#local)
- [Weex_加载本地图片](http://www.jianshu.com/p/3176329821f4)

原文: [weex android iOS 加载本地图片](http://www.sunzhongwei.com/weex-android-ios-loaded-local-pictures)