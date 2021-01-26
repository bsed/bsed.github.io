---
title: "Sublime Text插件:autoprefixer 自动添加兼容前缀"
categories: [ "Css" ]
tags: [ "Sublime Text3","autoprefixer" ]
draft: false
slug: "text-sublime-plugin-autoprefixer-automatically-adds-a-compatible-prefix"
date: "2015-08-04 09:09:00"
---

项目地址:autoperfixer
[https://github.com/sindresorhus/sublime-autoprefixer](https://github.com/sindresorhus/sublime-autoprefixer)

# autoperfixer 优点

1、可以定义兼容不同的浏览器加载不同的前缀，默认的是没有兼容IE/opera的-ms/-o，如果想要兼容参考github页面Options的设置。
2、批量替换，高效。
3、不依赖线上API， Sublime Prefixr就是因为依赖http://prefixr.com/ API，因为perfixr被墙，所以这个插件在大陆基本不能用。


<!--more-->


# autoperfixer 安装

***注意需要安装Node.js,并且将notejs加入到环境变量中**。

下载[github zip](https://github.com/sindresorhus/sublime-autoprefixer/archive/master.zip)
`Preferences -> Browse Packages` 将压缩包解压进去，文件夹根目录有autoprefixer.py即可。

# autoperfixer 快捷键设置

Preferences -> Key Bindings – User 新增

    [
        { "keys": ["ctrl+alt+shift+p"], "command": "autoprefixer" }
    ]

keys这个快捷的值可以根据自己的喜好修改。
