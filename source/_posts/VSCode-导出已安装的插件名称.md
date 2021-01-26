---
title: "VSCode 导出已安装的插件名称"
categories: [ "系统" ]
tags: [ "plugins","vscode","list-extensions" ]
draft: false
slug: "vscode-exports-the-name-of-the-installed-plugin"
date: "2020-12-01 17:51:00"
---

Windows10： %USERPROFILE%\.vscode\extensions
Mac Big Sur： ~/.vscode/extensions
Linux： ~/.vscode/extensions

https://github.com/grizzie17/vscode/blob/master/doc/VS-Code-Extensions.md

![vscode_cat_plugins.png][1]

您现在可以列出所有已安装的扩展，因此安装该扩展列表的方式是：
```bash
code --list-extensions >> vscode_extensions_list.txt
```
将新创建的文件传输到要安装这些扩展名的计算机。在那台机器上，(Windows10 下没有 cat 命令的，笔者windows 系统是可以的，[笔者电脑做了doskeys映射,类似linux里的 alias 别名])您将：
```bash
cat vscode_extensions_list.txt | xargs -n 1 code --install-extension
```
然后，它将遍历该文件中的每个扩展名并安装扩展名。

如果您希望全新安装（也就是删除该计算机上的所有现有扩展），则可以在安装新扩展之前运行它（否则，您也将删除这些新扩展）。请务必谨慎，因为这将删除VS Code中的所有扩展名：

code --list-extensions | xargs -n 1 code --uninstall-extension


  [1]: https://imgs.gnux.cn/usr/uploads/2020/12/395164051.png