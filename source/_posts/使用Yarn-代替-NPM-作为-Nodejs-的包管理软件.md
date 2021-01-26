---
title: "使用Yarn 代替 NPM 作为 Nodejs 的包管理软件"
categories: [ "JS" ]
tags: [ "nodejs","npm","yarn" ]
draft: false
slug: "use-yarn-instead-of-nodejs-as-the-npm-package-management-software"
date: "2016-10-24 21:44:00"
---

![yarn.png][1]
> Fackbook 推出了JavaScript的包管理器[Yarn](https://yarnpkg.com/)。研究小组还得到了,像Google,Tilde and Exponent的支持。让我科普一下, Node 的软件包管理器（NPM）。我们使用包管理器来查找和使用现有的代码或库。就像我们可以安装jQuery，VueJS，Redux的东西从包管理器和它的超级容易。

**yarn拥有以下特性**:

 1. 透过 yarn.lock ，锁住套件版本，因此可以确保安装之套件在每台机器上都能保持一致。
 2. 安装过的套件，都会加入到 `global cache` 中，下次有砍掉要重装，或是不同资料夹要装，都可以在无网路情况底下安装。
 3. 非常快，平行化处理每个 `operation`，全新的 `resolving` 演算法。

```bash
npm i packageName --save
```


<!--more-->


##全局安装yarn

```bash
npm install --global yarn
//简写
npm install -g yarn
// 直接安裝 (mac为例)
curl -o- -L https://yarnpkg.com/install.sh | bash
```
安装后查看版本

```bash
yarn --version
```

如果你没有安装了npm,检查网站文档以了解更多的细节。[https://yarnpkg.com/en/docs/install](https://yarnpkg.com/en/docs/install)

## 如何用yarn

```bash
//初始化档案库 package.json
npm init === yarn init
npm link === yarn link
npm outdated === yarn outdated
npm publish === yarn publish
npm run === yarn run
npm cache clean === yarn cache clean
npm login === yarn login
npm logout === yarn logout
npm test === yarn test
// 安裝特定插件 (等同 npm install --save)
yarn add react         
yarn add react@15.3.2
yarn add <https://thatproject.code/package.tgz>
yarn add file:/path/to/local/folder
yarn add <git remote-url>
npm install starwars --save : yarn add starwars
npm install starwars --global : yarn global add starwars
npm install starwars --save-dev : yarn add starwars --dev
npm uninstall starwars --save : yarn remove starwars
npm update --save : yarn upgrade
// 更新特定插件 (等同 npm upgrade)
yarn upgrade react
// 移除特定插件 (等同 npm uninstall)
yarn remove react
// 新增 package.json
yarn init
// 新增全局插件
yarn global add
// 跑 script
yarn run 
// 其他
--offline   (离线模式，只拉 cache)
--flat      (將套件扁平化，一个资料夹只会有一个插件)
--dev       (加入到 devDependencies)
--peer      (加入到 peerDependencies)
--optional  (加入到 optionalDependencies)
```

`Install` is Yarn  的默认行为

```bash
npm install === yarn
```

例如将 `React js` 库保存到 package.json 文件中

```bash
npm install react --save === yarn add react
```
yarn 拥有npm 没有的特性,例如:

```bash
yarn licenses
yarn licenses generate
yarn why react
```

Yarn 和 npm 的包仓库是同步的，所以 npm 的 `registry` 同样对 yarn 适用。执行以下命令就可以把 yarn 的源设置为 taobao 的 `npm registry`。但是我发现改了之后并没有加快 yarn 的速度多少，所以以下 yarn 使用的依旧是默认源。

```bash
yarn config set registry https://registry.npm.taobao.org
```

但是必须要说 yarn 的出现确实是有进步意义的，他解决了 npm 已知的一些问题，拓展出了新的特性。既然如此，它就是值得去接触和学习的。尽管距离取代 npm 还有好长的一段时间，走好长的一段路，但是可以预见它的前途将是一片的光明。
所以，来吧，你需要尝试一些新的东西。


  [1]: https://imgs.gnux.cn/usr/uploads/2016/10/934833976.png