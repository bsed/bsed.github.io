---
title: "virtualenv配置python虚拟空间"
categories: [ "Python" ]
tags: [ "python","workon","virtualenvwrapper" ]
draft: false
slug: "virtualenv"
date: "2017-12-13 16:12:00"
---

## 安装
通过默认pip全局安装
```bash
sudo pip3 install virtualenv
```
## 安装基础包

### 使用
因为懒，所以这里建议安装 `virtualenvwrapper`。

`virtualenvwrapper` 是 `virtualenv` 的扩展管理包， 用于更方便管理虚拟环境。

## virtualenvwrapper

### 安装
### 通过默认pip全局安装
```bash
sudo pip3 install virtualenvwrapper
```
在~/.bashrc中添加环境变量
```bash
export WORKON_HOME='~/.venvs'
export PIP_VIRTUALENV_BASE=WORKON_HOME 
#export PROJECT_HOME=$HOME/kelvin  
export VIRTUALENVWRAPPER_SCRIPT=/usr/share/virtualenvwrapper/virtualenvwrapper.sh
source /usr/share/virtualenvwrapper/virtualenvwrapper_lazy.sh
```
编辑保存完后，运行`source ~/.bashrc`，使设置生效

### 使用
可使用`virtualenvwrapper --help`查看所有命令方法

但是常用就下面这几个

创建环境：`mkvirtualenv` [环境名]
删除环境：`rmvirtualenv` [环境名]
激活环境：`workon` [环境名]
退出环境：`deactivate`
所有环境：`workon` / `lsvirtualenv -b`

### example:

当前环境安装有`Python2.7`和`Python3.5`，想自由切换Python运行环境
先设置好虚拟环境

```bash
mkvirtualenv -p /usr/bin/python2 cv2 # cv2就是你设置的环境名
mkvirtualenv -p /usr/bin/python3 cv3 # cv3就是你设置的环境名
```
列出所有虚拟环境，可以得到下面信息

$ workon
cv2
cv3
运行`workon cv2`，就切换当前环境为Python2.7

运行`workon cv3`，就切换当前环境为Python3.5