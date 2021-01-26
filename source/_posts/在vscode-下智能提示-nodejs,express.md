---
title: "在vscode 下智能提示 nodejs,express"
categories: [ "JS" ]
tags: [ "nodejs","vscode","typescript" ]
draft: false
slug: "using-express-nodejs-under-code-vs"
date: "2016-05-18 19:20:00"
---

vs code和sublime的快捷键基本类似。用起来很顺手。代码的智能提示很简单，就是使用[tsd](http://definitelytyped.org/tsd/)

## 使用npm安装: 

```bash
$ npm install tsd -g​
```
tsd安装后在代码文件夹目录下执行：
```bash
$tsd install node 
$tsd install express​
$tsd install morgan​
$tsd install body-parser​
```
或者`$tsd install node express`

或者`tsd query -r -o -a install node angular express`

或者`tsd query node --action install`
<!--more-->


会下载node​.d.ts和express.d.ts文件在typings文件夹下。然后重启vs code 就可以实现代码提示了。
![vscode_typescript_suggest.png][1]
或者，在代码的头部，手动增加 reference 按下tab 键 自动添加, 如果提示不全的话，去掉最后一个`/` 再次按下`/`就好了
```
/// <reference path="./typings/node/node.d.ts" />
/// <reference path="./typings/express/express.d.ts" />
/// <reference path="./typings/morgan/morgan.d.ts" />
/// <reference path="./typings/body-parser/body-parser.d.ts" />
```
也可以实现代码提升，这里的path就是你的.d.ts文件的路径。.d.ts文件可以从tsd官网找到并下载

需要注意的是 需要**重启 vscoe** .

  [1]: https://imgs.gnux.cn/usr/uploads/2016/05/355672704.png