---
title: "Node.js教程入门-开始使用Node Js [Node Hero]"
categories: [ "JS" ]
tags: [ "nodejs","Node Hero" ]
draft: false
slug: "nodejs-tutorial-entry-node-hero"
date: "2017-01-04 11:02:00"
---

> 这是Node.js教程系列的第一篇文章，称为`Node Hero` -
> 在这一章节中，您可以了解如何开始使用Node.js并使用它开发web产品。

在这个第一个教程中，您将了解`Node.js`是什么，如何在计算机上安装它以及如何开始使用它 - 因此在接下来的内容中我们可以做实际开发。让我们开始吧！

## 什么是Node.js

[node_js_logo_in_node_hero_getting_started_tutorial.webp][1]
这是nodejs的官方logo

Node.js是一个基于Chrome的 `V8` JavaScript引擎的JavaScript运行时环境。 Node.js使用事件驱动的非阻塞I / O模型，使其轻量级和高效。

**换句话说**：Node.js可以让您使用JavaScript编写`高性能的服务器程序。官方声明曾说过：`Node.js`是一个运行时使用相同的V8 JavaScript引擎（谷歌浏览器浏览器可以找到）。但是这不足以说明JS的成功，Node.js利用`libuv`，与`异步I/O`实现多平台支持库。
[libuv_logo_in_node_hero_getting_started_tutorial.webp][2]

从开发人员的角度来看，Node.js是单线程的，但是在内核下，`libuv` 处理线程(threading)，文件系统事件(file system events)，实现事件循环( event loop)，特性线程池(features thread pooling)等。在大多数情况下，你不会直接与它进行交互。

## 安装Node JS

要获得最新的Node.js二进制文件，您可以访问Node.js官方网站：[https：//nodejs.org/en/download/](https：//nodejs.org/en/download/)

使用这种方法，它很容易安装 - 但是如果后来你想添加更多的`Node.js版本`，最好开始使用**[nvm](https://github.com/creationix/nvm)**，版本管理器。

安装后，您可以使用非常简单的CLI API，您可以与之进行交互：

### 安装Node JS 版本
```bash
nvm install 4.4  
```

然后，如果你想安装实验版本：
```bash
nvm install 5  
```

要验证您的NodeJS 是否安装正确，请运行以下命令：
```bash
node --version  
```
如果一切正常，它将返回当前安装的Node.js二进制文件的版本号。

## 切换Node.js版本
如果您正在开发支持 `Node.js v4`的项目，则可以使用以下命令开始使用它：
```bash
nvm use 4  
```

然后,你可以用非常相同的命令切换到`Node.js v5`
```bash
nvm use 5  
```

好了，现在我们知道如何在Node.js版本之间安装和切换 - 但是有什么意义呢？

因为Node.js基金会已经发布计划。这意味着有两个版本：稳定版本和实验。在Node.js长期支持的稳定版本（LTS）是从偶数（4，6，8…）和实验版是奇数（5，7…）。我们建议您使用生产的LTS版本和尝试新的事物与实验

如果你使用的是 Windows系统，有一个替代nvm：[nvm-windows](https://github.com/coreybutler/nvm-windows)。

## 编写 Hello World

要开始使用Node.js，让我们在bash里试试吧！键入`node`命令后，启动Node.js
```bash
$ node
>
```

让我们打印一条消息吧。
```bash
$ node
> console.log('hello world, Node.js')
```

按下`Enter`键，你会得到如下内容

```bash
> console.log('hello world, Node.js')
hello from Node.js  
undefined  
```

通常我们可以使用bash下 键入`node`，尝试一些小片段，这样就可以不用把它存到文件里面。
现在是创建我们的`Hello World` Node.js应用程序的时候了！

让我们开始创建一个名为`index.js`的文件。打开你的IDE（Atom，Sublime，VSCode等），创建一个新文件并保存为`index.js`。如果您已完成此操作，请将以下代码段复制到此文件中：

```javascript
// index.js
console.log('hello from Node.js')  
```
要运行此文件，您应该再次打开终端并导航到`index.js`所在的目录。

一旦你成功地导航到正确的地方，使用`node index.js`命令运行你的文件。您可以看到它将产生与以前相同的输出：
将字符串直接打印到终端。

## 模块化开发

现在你有了一个`index.js`文件，如果你有很多文件该怎么办呢？ 让我们通过将源代码拆分为多个JavaScript文件来创建更复杂的东西，以提高可读性和可维护性。回到你的IDE（Atom，Sublime，Code等），并创建以下目录结构（使用空文件）

```bash
├── app
|   ├── calc.js
|   └── index.js
├── index.js
└── package.json
```

每个`Node.js`项目 都是以 创建ackage.json文件开始的。你可以认为这是是项目所依赖的JSON序列和其他信息。它包含您的应用程序的名称、作者信息，和运行该应用程序所需的所有依赖项。我们将在后面在Node Hero的**Using NPM**一章中讨论NPM的依赖关系

你可以在bash终端下使用 `NPM init`命令生成你的`package.json`文件。点击输入后你会要求给出几个输入，比如你的应用程序的名称，版本，描述等等。不用担心，如果不想给程序添加相关信息的话，只要按下回车键就可以啦。
如果在IDE中打开该文件，它将看起来非常类似于下面的代码片段
```
{
  "name": "@bsed/node-hero",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node index.js"
  },
  "author": "bsed",
  "license": "MIT"
}
```

这是一个很好的例子，添加一个启动脚本到你的`package.json` -一旦你做了，如图所示，在上面的例子中，你可以使用`npm start`命令 启动你的应用。当您想将您的应用程序部署到`PaaS`提供商，他们可以识别和使用，启动您的应用程序。


现在让我们回到你所创建的第一个文件`index.js`。打开它，复制下面的脚本文件，点击保存到您的`index.js`。
```bash
// index.js
require('./app/index')  
```
是时候开始构建真实的应用程序了,现在让我们创建一个简单的例子。从`/app` 文件目录下打开`index.js`文件, 添加一个numbers 类型的数组到`index.js`文件下，计算的逻辑需要放在单独的模块中。

将此脚本粘贴到`/app`目录中的`index.js`文件里面
```javascript
// app/index.js
const calc = require('./calc')

const numbersToAdd = [  
  3,
  4,
  10,
  2
]

const result = calc.sum(numbersToAdd)  
console.log(`The result is: ${result}`)  
```

现在将下面的实际业务逻辑粘贴到`/app`目录中的 `calc.js`文件中。
```javascript
// app/calc.js
function sum (arr) {  
  return arr.reduce(function(a, b) { 
    return a + b
  }, 0)
}

module.exports.sum = sum  
```

检查如果你成功的保存这些文件，打开终端，输`入npm start` or `node index.js`。如果你做得对，你会得到答案：`19`。如果出了问题，仔细检查控制台日志，然后根据问题找到问题并解决掉。

  [1]: https://imgs.gnux.cn/usr/uploads/2017/01/3806247023.webp
  [2]: https://imgs.gnux.cn/usr/uploads/2017/01/3922879215.webp