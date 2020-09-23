---
title: ReactNative之package.json文件详解
date: 2017-02-10 15:16:00
updated: 2017-02-21 11:27:51
tags: 
- php
- Mobile-Detect
categories: 
- php

---
`package.json`文件描述了一个npm包的所有相关信息，包括作者、简介、包依赖、构建等信息。格式必须是*严格的json格式*。

通常我们在创建一个npm程序时，可以使用npm init命令，通过交互式的命令，自动生成一个package.json文件，里面包含了常用的一些字段信息，但远不止这么简单。通过完善package.json文件，我们可以让npm命令更好地为我们服务。

## name

name和version是package.json中最重要的两个字段，也是发布到npm平台上的唯一标识，如果没有正确设置这两个字段，包就不能发布和被下载。

下面是官方给出的一些建议:

名字里不要再包含”js”和”node”，因为默认NPM包就是node.js程序，不过你可以通过engines字段来指定。
名字将会被作为url的一部分，所有要符合http url的一般命名规则，不能包含url非法字符，也不能以`.`和`_`开头。
名字也将作为require()命令的参数，所以应该尽量简明。
如果包要发布到NPM平台上的话，最好先检查下有没有重名, 并且字母只能全部小写。
新版本的NPM可以指定scope, 名字可以加前缀标识，如@ijse/mypackage。


<!--more-->


## version

这个字段的取值需要符合[node-semver](https://github.com/isaacs/node-semver)的规则，详细可以见其文档

## scripts

通过设置这个可以使npm调用一些命令脚本，封装一些功能。

## dependencies

指定依赖的其它包，这些依赖是指包发布后正常执行时所需要的，如果是开发中依赖的包，可以在devDependencies设置。

通常使用下面命令来安装：
```
npm install otherpackage --save
```
形式可以有如下多种：
```
version 严格匹配某个版本
>version 必须大于某个版本
>=version
<version
<=version
~version 大概匹配某个版本
^version 兼容某个版本
1.2.x 可以是1.2.0, 1.2.1等等，但不能是1.3.0
http://... 指定tarball的url地址
* 任何版本都可以
"" 同上
version1 - version2 >=version1 <=version2
range1 || range2 满足range1 或range2
git://... git地址
user/repo 同上
tag 指定某个tag的版本
path/path 本地包所有文件夹
```
下面都是可以用的：
```json
{ "dependencies" :
  { "foo" : "1.0.0 - 2.9999.9999"
  , "bar" : ">=1.0.2 <2.1.2"
  , "baz" : ">1.0.2 <=2.3.4"
  , "boo" : "2.0.1"
  , "qux" : "<1.0.0 || >=2.3.1 <2.4.5 || >=2.5.2 <3.0.0"
  , "asd" : "http://asdf.com/asdf.tar.gz"
  , "til" : "~1.2"
  , "elf" : "~1.2.3"
  , "two" : "2.x"
  , "thr" : "3.3.x"
  , "lat" : "latest"
  , "dyl" : "file:../dyl"
  }
}
```
git URL可以有如下种形式：
```bash
git://github.com/user/project.git#commit-ish  
git+ssh://user@hostname:project.git#commit-ish  
git+ssh://user@hostname/project.git#commit-ish  
git+http://user@hostname/project/blah.git#commit-ish  
git+https://user@hostname/project/blah.git#commit-ish
```
## devDependencies

这些依赖只有在开发时候才需要。
```bash
npm install --save-dev mypack
```