---
title: Yeoman学习笔记
date: 2015-01-15 10:30:00
updated: 2017-01-15 10:56:27
tags: 
- activemq
- stomp-js
categories: 
- js

---
> Yeoman是Google的团队和外部贡献者团队合作开发的，他的目标是通过Grunt（一个用于开发任务自动化的命令行工具）和Bower（一个HTML、CSS、Javascript和图片等前端资源的包管理器）的包装为开发者创建一个易用的工作流。

Yeoman的目的不仅是要为新项目建立工作流，同时还是为了解决前端开发所面临的诸多严重问题，例如零散的依赖关系。

[Yeoman][1]主要有三部分组成：[yo][2]（脚手架工具）、[grunt][3]（构建工具）、[bower][4]（包管理器）。这三个工具是分别独立开发的，但是需要配合使用，来实现我们高效的工作流模式。

##准备工作


<!--more-->


检查系统中是否安装了：Node.js、Ruby、Compass。

Mac下安装Node.js非常方便，首页提供了一个pkg下载，双击后可以默认安装node、npm到`/usr/local/bin`下，我们只需要确保 `/usr/local/bin` 包含在PATH变量中就可以。

Mac Lion 下自带了`Ruby`，所以也就不需要再单独安装了。

Compass安装需要依赖于Ruby Gems，执行下面的步骤：
```bash
sudo gem update --system
sudo gem install compass
```
## 安装

环境准备好之后，就可以进行安装了，执行：
```bash
sudo npm install -g yo grunt-cli bower
```

## 使用

执行 `yo webapp` 我们就可以快速的创建一个新的Web应用的框架，将近有400多个[生成器框架][5]， 比较受欢迎的有[generator-angular][6]、[generator-backbone][7]、[generator-ember][8]。


参考文章：

 - http://blog.jobbole.com/65399/
 - http://blog.fens.me/nodejs-yeoman-intro/

  [1]: http://yeoman.io/
  [2]: http://yeoman.io/
  [3]: http://gruntjs.com/
  [4]: http://bower.io/
  [5]: http://yeoman.io/community-generators.html
  [6]: https://github.com/yeoman/generator-angular
  [7]: https://github.com/yeoman/generator-backbone
  [8]: https://github.com/yeoman/generator-ember