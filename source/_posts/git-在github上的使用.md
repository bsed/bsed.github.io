---
title: "git 在github上的使用"
categories: [ "Linux" ]
tags: [ "github" ]
draft: false
slug: "github-git-tutor"
date: "2014-11-16 11:52:00"
---

### Git概念 ### 
Git库中由三部分组成 
       Git 仓库就是那个.git 目录，其中存放的是我们所提交的文档索引内容，Git 可基于文档索引内容对其所管理的文档进行内容追踪，从而实现文档的版本控制。.git目录位于工作目录内。 
1） 工作目录：用户本地的目录； 
2） Index（索引）：将工作目录下所有文件（包含子目录）生成快照，存放到一个临时的存储区域，Git 称该区域为索引。 
3） 仓库：将索引通过commit命令提交至仓库中，每一次提交都意味着版本在进行一次更新。 


在Git中运行git bash，然后依次执行下列命令，配置你的git，代码如下：

    git config --global user.name "用户名"
    git config --global user.email "邮箱"
    git config --global credential.helper cache
    git config --global credential.helper 'cache --timeout=3600'

执行代码效果如下图

创建一个SSH Keys，输入下列语句，看看本地有没有ssh keys。

    cd ~/.ssh

显示如下图表示没有。
接下来创建SSH Keys，执行下列代码。

    ssh-keygen -t rsa -C "邮箱"
然后会要你选择保存的位置，直接回车即可，如下图：
接下来还要输入密码，如下图：
完成后，你就可以在你刚才保存的文件路径中看到一个id_rsa.pub文件了，公钥就在这里
添加SSH Keys成功连接Github
1、用记事本打开id_rsa.pub，复制里面的东西，粘贴到Github项目的SSH Keys中，如下图（点击放大）：
输入以下代码，测试是否可以成功连接。
第一次连接还要输入Yes，确认一下，如下图：


## 解决git无法clone提示443以及配置git代理方法
```bash
git config --global http.proxy "localhost:1080"
git config --global https.proxy "localhost:1080"
```

或者
```bash
git config --global https.proxy http://127.0.0.1:1080
git config --global https.proxy https://127.0.0.1:1080
```
 

## 默认不设置代理：

 

git config --global --unset http.proxy
git config --global --unset https.proxy

觉得还可以的话记的打赏哦^.^..
```

