---
title: TmodJS：前端模板预编译工具
date: 2014-08-29 13:44:00
updated: 2014-09-11 22:07:28
tags: 
- css3
- ie8
categories: 
- css

---
    TmodJS（原名atc）是一个简单易用的前端模板预编译工具，通过预编译技术让前端模板突破浏览器限制，实现后端模板一样的同步“文件”加载能力，预编译输出的代码经过多层优化，可最大程度节省客户端资源消耗。

TmodJS（原名atc）是一个简单易用的前端模板预编译工具。它通过预编译技术让前端模板突破浏览器限制，实现后端模板一样的同步“文件”加载能力。它采用目录来组织维护前端模板，从而让前端模板实现工程化管理，最终保证前端模板在复杂单页Web应用下的可维护性。同时预编译输出的代码经过多层优化，能够最大程度节省客户端资源消耗。


<!--more-->


TmodJS一经启动，就无需人工干预，每次模板创建与更新都会自动编译，引入一个JS即可使用template(path) 接口调用本地模板文件，直到正式上线都无需对代码进行任何修改，整个过程简单自然。

TmodJS主要功能列表：

    编译模板为不依赖引擎的JavaScript文件
    前端模板按文件与目录组织
    模板之间支持引入外部模板
    使用同步模板加载接口
    可选多种规范的模块输出：AMD、CMD、CommonJS
    模板目录可被前后端共享
    支持检测修改即时编译
    支持模板运行时调试
    配置文件支持多人共享 

同时，TmodJS可以作为Grunt和Gulp的一个插件使用，TmodJS是一个本地工具，不需要部署到服务器。安装TmodJS，只需执行 npm install -g tmodjs 命令即可。

项目Github地址：https://github.com/aui/tmodjs
CSDN CODE镜像地址：https://code.csdn.net/Tencent/tmodjs

目前TmodJS最新版本为1.0.1，授权协议为BSD。

相关链接：

    腾讯开源项目：http://code.tencent.com/
    腾讯开源专题：http://code.csdn.net/zone/tencent