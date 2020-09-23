---
title: vscode显示空格
date: 2015-02-25 16:23:00
updated: 2017-03-07 16:25:32
tags: 
- gtags
categories: 
- linux

---
vscode默认的开发环境，不显示空格，并且tab键也不是四个空格，所以需要设置。
方法:

修改文件的位置在软件的`file`-->`preferences`-->`usersetting`
打开后，会出现两个页面，左面为软件默认default，右边为用户区，左边是不可修改的，只能在右边修改。
在右边的用户设置区添加如下内容：


<!--more-->


```json
// Place your settings in this file to overwrite the default settings
{
	"editor.tabSize": 4, //tab为4个空格
	"editor.fontSize": 18, //字体大小
	"editor.renderWhitespace": true, 显示空格
	"editor.insertSpaces": true, //按tab键插入空格
    "files.exclude": { //在vscode中不显示某些文件
        "**/*.pyc":true
	}
}
```
保存即可。