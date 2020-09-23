---
title: Windows，NodeJS 环境配置
date: 2014-09-06 10:49:00
updated: 2014-11-03 13:32:06
tags: 
- nodejs
- windows
categories: 
- css

---
真的没见过比 NodeJS 更简单的开发环境搭建了，打开 [http://nodejs.org/download/][1] 下载适合你的32位或者64位 Windows Installer (.msi) 安装包，双击一路狂点下一步，完成！
WIN + R 键入 CMD 运行，输入 `node -v` ，成功显示 NodeJS 版本即成功，输入 `npm -v` 显示 `npm` 版本号。
现在，你可以使用 `npm install MODULE_NAME` 安装你需要包进行开发了。
满足你的强迫症
<!--more-->
如果你有以下疑问:
nodejs 这货装哪去了？
我运行 `npm install -g` 命令时，这个模块被保存在哪里？
听说 npm 有 cache 机制，这些 cache 保存在哪里？
我先回答你：
nodejs 默认安装在 `C:\Program Files\nodejs` ，如果是 64 位的系统，可能在 `C:\Program Files (x86)\nodejs
npm` 默认全局安装的模块保存在 `C:\Users\UserName\AppData\Roaming\npm` 目录下
cache 这玩意我也不知道它装哪去了
我相信你的C盘应该够用，而且这些模块不是微软开发的，绝对不大。
如果你真的要折腾，接着看吧。
创建目录 `E:\node` 、接着创建 `c:\node\npm-cache`，当然，你可以换到任意的盘符
复制 C:\Program Files\nodejs 到 E:\node\nodejs，复制 C:\Users\UserName\AppData\Roaming\npm 到 E:\node\npm
创建文件 .npmrc 保存到 C:\Users\UserName\.npmrc，

    prefix = E:\node\npm
    cache = E:\node\npm-cache

打开环境变量 path，找到 C:\Program Files\nodejs 替换成 E:\node\nodejs。找到 E:\Users\UserName\AppData\Roaming\npm 替换成 E:\node\npm。
创建环境变量 NODE_PATH，值为 E:\node\npm\node_modules
大概就是这样，如果修改完发现 `node -v` 等跑不鸟，请使用重启大法
写到最后，我突然感觉到，其实只有我才有这样的强迫症，当年不把这些目录搞清楚，没法开始写 NodeJS。


  [1]: http://nodejs.org/download/