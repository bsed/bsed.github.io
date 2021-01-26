---
title: "Bower：浏览器端库管理工具"
categories: [ "JS" ]
tags: [ "js","bower" ]
draft: false
slug: "bower-browser-side-library-management-tool"
date: "2015-08-07 09:19:21"
---

> 随着网页功能变得越来越复杂，同一张网页加载多个JavaScript函数库已经很常见。开发者越来越需要一个工具，对浏览器端的各种库进行管理，像NPM一样，Bower就是为了解决这个问题而诞生的针对浏览器端的库管理工具。

Bower基于node.js，所以安装之前，必须先确保已安装node.js。

### 安装 bower
```shell
npm install bower -global
```
### 使用 bower
帮助


<!--more-->


```shell
bower help
```
安装库
```shell
bower install jquery
```
默认情况下，会安装该库的最新版本，但是也可以手动指定版本号。
```shell
bower install jquery-ui#1.10.1
```
上面的命令指定安装jquery-ui的1.10.1版。

如果某个库依赖另一个库，安装时默认将所依赖的库一起安装。比如，jquery-ui依赖jquery，安装时会连jquery一起安装。

安装后的库默认存放在项目的bower_components子目录，如果要指定其他位置，可在.bowerrc文件的directory属性设置。

库的查找
```shell
bower search jquery
```

查看库的信息
```shell
bower info jquery-ui
```

更新库
```shell
bower update angularjs
```

卸载库
```shell
bower uninstall angularjs
```

列出当前安装的库
```
bower list
```

#### bower 配置文件
.bowerrc

```json
{
  "directory" : "components",
  "json"      : "bower.json",
  "endpoint"  : "https://Bower.herokuapp.com",
  "searchpath"  : "",
  "shorthand_resolver" : ""
}
```

- directory：存放库文件的子目录名。
- json：描述各个库的json文件名。
- endpoint：在线索引的网址，用来搜索各种库。
- searchpath：一个数组，储存备选的在线索引网址。如果某个库在endpoint中找不到，则继续搜索该属性指定的网址，通常用于放置某些不公开的库。
- shorthand_resolver：定义各个库名称简写形式。

bower.json
```json
{
  "name": "app-name",
  "version": "0.1.0", 
  "dependencies": {
    "sass-bootstrap": "~3.0.0",
    "modernizr": "~2.6.2",
    "jquery": "latests"
  }
}
```

http://javascript.ruanyifeng.com/tool/bower.html

### http-server
一个简单的零配置http server，用于快速前端开发

#### 使用方法
```shell
usage: http-server [path] [options]

options:
  -p                 Port to use [8080]
  -a                 Address to use [0.0.0.0]
  -d                 Show directory listings [true]
  -i                 Display autoIndex [true]
  -e --ext           Default file extension if none supplied [none]
  -s --silent        Suppress log messages from output
  --cors             Enable CORS via the 'Access-Control-Allow-Origin' header
  -o                 Open browser window after staring the server
  -c                 Cache time (max-age) in seconds [3600], e.g. -c10 for 10 seconds.
                     To disable caching, use -c-1.

  -S --ssl           Enable https.
  -C --cert          Path to ssl cert file (default: cert.pem).
  -K --key           Path to ssl key file (default: key.pem).

  -h --help          Print this list and exit.
```
https://github.com/nodeapps/http-server