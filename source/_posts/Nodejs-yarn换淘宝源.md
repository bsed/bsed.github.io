---
title: "Nodejs yarn换淘宝源"
categories: [ "JS" ]
tags: [ "nodejs","yarn" ]
draft: false
slug: "nodejs-yarn-for-taobao-source"
date: "2017-02-12 20:38:29"
---

如果觉得安装速度慢，安装源和原来 npm 是一样的，可以通用，修改方法如下：
```bash
yarn config get registry  
# -> https://registry.yarnpkg.com
```
可以改成 taobao 的源：
```bash
yarn config set registry 'https://registry.npm.taobao.org'  
# -> yarn config v0.15.0
# -> success Set "registry" to "https://registry.npm.taobao.org".
# -> Done in 0.04s.
```
然后 `yarn install` 的速度就快多了