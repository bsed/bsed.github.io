---
title: "自制node.js + npm绿色版"
categories: [ "JS" ]
tags: [ "nodejs","npm","green" ]
draft: false
slug: "homemade-nodejs-npm-green-edition"
date: "2017-07-12 09:57:00"
---

Node.js [官网](http://nodejs.org/) 有各平台的安装包 [下载](http://nodejs.org/download) ，不想折腾的可以直接下载安装，下面说下windows平台下如何制作绿色版node，以方便迁移。

1. 获取 [node.exe](http://nodejs.org/download) 
   下载Windows Binary版本，不要下载Windows Installer版本，直接放到`H:\nodejs\`
2. 获取 [npm](http://nodejs.org/dist/npm) （Node Package Manage） 
   下载最新zip版本，不要下载tgz版本，下载后解压到`H:\nodejs\`
3. 添加环境变量
   - `NODE_HOME=H:\nodejs`
   - `NODE_PATH=%NODE_HOME%\node_modules`
   - `path增加%NODE_HOME%\`

最终得到文件目录结构H:\nodejs\：

```
.
├── node.exe
├── npm.cmd
└── node_modules
    └── npm
```

测试一下，出现版本号，说明配置成功：

```
node --version
npm --version

```


<!--more-->


> 至此，绿色版制作完毕，您已经可以正常使用node/npm，也可以迁移到其他机器使用。

下面使用npm命令安装第三方模块，使用方法可以查看：

```
npm -h
npm install -h
npm help install
```

默认安装仓库是 [https://registry.npmjs.org](https://registry.npmjs.org/) ，查找包可以到这里 [http://search.npmjs.org](http://search.npmjs.org/) ，一切都很像maven。有两种安装模式可选，参考 [npm 1.0 Global vs Local installation](http://blog.nodejs.org/2011/03/23/npm-1-0-global-vs-local-installation) ： 
**locally** 
默认是locally模式，安装到当前命令的执行目录。在其他位置执行xxx会报 *“xxx不是内部或外部命令，也不是可运行的程序”* 。

```
npm install xxx
```

**globally** 
-g参数代表全局方式，使用全局模式会安装到 `H:\nodejs\node_modules\` 中的xxx下。

```bash
npm install xxx -g
```

**重新设置prefix/cache位置后，需要在path 下添加相关引用，具体见下面修改:**

你可以重新设置prefix位置，方法有二：

- 重新设置prefix的位置：`npm config set prefix "H:\nodejs\node_global"`
- 或直接修改 『当前用户』.npmrc 文件，添加`prefix = H:\nodejs\node_global`

你可以重新设置cache位置，方法有二：

- 重新设置cache的位置：`npm config set prefix "H:\nodejs\node_cache"`
- 或直接修改 『当前用户』.npmrc 文件，添加`prefix = H:\nodejs\node_cache`

环境变量修改为如下：
- `NODE_HOME=H:\nodejs`
   - `NODE_PATH=%NODE_HOME%\node_global\node_modules`
   - `path增加%NODE_HOME%\;%NODE_PATH%`