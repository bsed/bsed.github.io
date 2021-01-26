---
title: "vscode Golang开发环境配置"
categories: [ "Golang" ]
tags: [ "golang","vscode" ]
draft: false
slug: "golang-vscode-development-environment-configuration"
date: "2016-11-03 20:56:00"
---

本文主要介绍的是如何使用VS Code 搭建一个进行Go开发的IDE。

## VS Code 介绍
VS Code 是微软开源的一个编辑器软件。刚面世的时候侧重于 C# 和前端开发，随着版本迭代和新功能出现，特别是支持安装第三方插件，VS Code 也支持其它语言的开发。

​VS Code 原生支持智能提示、方法补全的语言有：JavaScript, JSON, HTML, CSS, LESS, SASS。如果装上下文提到的 Go 插件，也是能够支持 Go 语言的智能提示和方法补全。

VS Code 最牛逼的地方是 它和 Atom 都是基于 Electron 构建的应用，但是相比于 Atom 常被诟病的卡卡卡，VS Code 打开超大文件几乎秒开。

不足的地方是 VS Code 的第三方插件的丰富度不及 Atom，但是假以时日，随着更多人关注它，VS Code 的第三方插件肯定会越来越丰富。

## 下载Visual Studio Code并安装，下载地址：

[https://www.visualstudio.com/en-us/products/code-vs.aspx](https://www.visualstudio.com/en-us/products/code-vs.aspx)


## 安装相关插件

安装各种 go tool，用以`go get -u -v <tool url>`命令來安裝,因为大多数go tool都会从golang.org获取内容，所以请*翻墙*：
```bash
go get -u -v github.com/nsf/gocode  
go get -u -v github.com/rogpeppe/godef  
go get -u -v github.com/golang/lint/golint  
go get -u -v github.com/lukehoban/go-find-references  
go get -u -v github.com/lukehoban/go-outline  
go get -u -v sourcegraph.com/sqs/goreturns  
go get -u -v golang.org/x/tools/cmd/gorename
go get -u -v github.com/tpng/gopkgs
go get -u -v github.com/newhook/go-symbols
go get -u -v golang.org/x/tools/cmd/guru
go get -u -v golang.org/x/tools/cmd/goimports
go get -u -v github.com/derekparker/delve/cmd/dlv  
```  
## 安装 vscode go 插件

打开vs code，同时按`command + shift + p`，输入`extension install`，找到*go*插件，现在最新的版本是0.6.10。
![vscode_go01.png][1]

**拥用的特性：**

 - 彩色高亮Colorization
 - 自动完成列表 (using gocode)
 - 方法和类的签名帮助信息 (using godoc)
 - 代码片段
 - 快速信息 (using godef)
 - 查找定义 (using godef)
 - 查找引用 (using guru)
 - 文件大纲 (using go-outline)
 - 工作区符号搜索 (using go-symbols)
 - 重命名 (using gorename)
 - 保存时编译 (using go build and go test)
 - 格式化 (using goreturns or goimports or gofmt)
 - 增加导入 (using gopkgs)
 - 调试 [部分实现] (using delve)

重启后，打开一个`.go` 格式的文件就可以看到有提示功能了！
![vscode_go02.png][2]

vscode的Go插件有一些可以定制化的配置，你可以打开`user preferences` 或者 `workspace settings`来配置。如果想对所有的Go项目有效，就修改`user preferences`。

点击菜单 `File -> Preferences -> User Settting` 打开窗口，左边的窗口是 vscode缺省配置，右边的窗口可以添加我们自己的设置。对于Go插件，可以添加如下的设置：
```json	
{
  "go.formatTool": "go fmt",
  "go.buildOnSave": false,
  "go.lintOnSave": true,
  "go.vetOnSave": true,
  "go.buildTags": "",
  "go.buildFlags": [],
  "go.lintFlags": [],
  "go.vetFlags": [],
  "go.coverOnSave": false,
  "go.useCodeSnippetsOnFunctionSuggest": true,
  "go.formatOnSave": true,
  "go.formatTool": "goreturns",
  "go.goroot": "/home/kelvin/go",
  "go.gopath": "/home/kelvin/gocode",
  "go.gocodeAutoBuild": false,
   "files.autoSave": "afterDelay"
}
```
比如你可以改格式化工具为"gofmt",如 "go.formatTool": "gofmt"。

## 快捷键

你可以通过菜单 `File -> Preferences -> Keyboard Shortcuts` 查看和定义快捷键。左边的窗口显示缺省的快捷键设置，但是不是很直观，建议看这个链接，它分门别类的介绍了每个快捷键的功能。

一般涉及到快捷键，我都会对其中的一个快捷键特别的关注，就是 "ctrl + space"，这个键一般用来设置触发提示的功能，但是和输入法切换的快捷键冲突，所以我会重新定义它。在刚才打开的右边窗口中增加定义

```json
{
    "key": "ctrl+j",
    "command": "editor.action.triggerSuggest",
    "when": "editorTextFocus"
}
```

格式化代码的快捷点，默认设置为 `shift+alt+f`,我新增加了一个快捷键：
```json
{
       "key": "ctrl+alt+f",
       "command": "editor.action.format",
       "when": "editorTextFocus"
   }
```

以及执行测试的快捷键
```json
{
    "key": "ctrl+alt+t",
    "command": "go.test.cursor",
    "when": "editorTextFocus"
},
{
    "key": "ctrl+alt+p",
    "command": "go.test.package",
    "when": "editorTextFocus"
}
```
右边窗口的的右下角有对话框可以帮助你增加快捷键，当然你也可以想我这样一样手工添加。

**Mac**

```
[
    {
        "key": "cmd+shift+[",
        "command": "workbench.files.action.openPreviousWorkingFile"
    },
    {
        "key": "cmd+shift+]",
        "command": "workbench.files.action.openNextWorkingFile"
    },
    {
        "key": "cmd+w",
        "command": "workbench.files.action.closeFile"
    }
]
```

**Windows / Linux:**

```
[
    {
        "key": "win+shift+[",
        "command": "workbench.files.action.openPreviousWorkingFile"
    },
    {
        "key": "win+shift+]",
        "command": "workbench.files.action.openNextWorkingFile"
    },
    {
        "key": "win+w",
        "command": "workbench.files.action.closeFile"
    }
]
```

## 安装 delve

获取调试器delve的源码：

```bash
https://github.com/derekparker/delve.git  
```
如果你的系统是linux，那么只要进入代码目录 `make install` 即可。

**如果你是mac os**，那么要麻烦一些：

制作一个自签名的钥匙串

```bash
Open application “Keychain Access” (/Applications/Utilities/Keychain Access.app)  
Open menu /Keychain Access/Certificate Assistant/Create a Certificate...  
Choose a name (dlv-cert in the example), set “Identity Type” to “Self Signed Root”, set “Certificate Type” to “Code Signing” and select the “Let me override defaults”. Click “Continue”. You might want to extend the predefined 365 days period to 3650 days.  
Click several times on “Continue” until you get to the “Specify a Location For The Certificate” screen, then set “Keychain to System”.  
If you can't store the certificate in the “System” keychain, create it in the “login” keychain, then export it. You can then import it into the “System” keychain.  
In keychains select “System”, and you should find your new certificate. Use the context menu for the certificate, select “Get Info”, open the “Trust” item, and set “Code Signing” to “Always Trust”.  
[At least on Yosemite:] In keychains select category Keys -> dlv-cert -> right click -> GetInfo -> Access Control -> select "Allow all applications to access this item" -> Save Changes.
You must quit “Keychain Access” application in order to use the certificate and restart “taskgated” service by killing the current running “taskgated” process. Alternatively you can restart your computer.  
Run the following: CERT=dlv-cert make install, which will install the binary and codesign it.  
```
中文版：

 1. 菜单->钥匙串访问->证书助理->创建证书 证书名填dlv-cert，之后勾选[让我覆盖这些值]
 2. 把365天改成3650  
 3. 之后一路continue，直到选择请指定钥匙串以便保存证书  选择[系统]
 4. 点击创建完成  
 5. 在钥匙串管理里搜索刚才创建的证书，双击，修改代码签名选项为始终信任  
 6. 再到到dlv-cert的两个密钥，如果可以的话，选择访问控制，然后选择最大权限(在我的EL Capitan系统里这个选项始终改不了，不知道为什么)，输入管理员用户名密码，完毕。  

完成上述步骤后：

```bash
$ CERT=mycert make install
```
之后打开vscode，打开你的项目，打上断点，之后再在左边的 debug 栏点击运行(可能需要输入密码)，之后就可以看到断点处的变量值、函数调用栈等调试信息了。

## 设置DEUBG

切換至 Debug 模式：
![vscode_go03.png][3]
现在按下左上方的 Play 按鈕，并选择 Go 作为 Debug 环境：
![vscode_go04.png][4]
接着就可以开始 断点调试了！
![vscode_go05.png][5]


## 常用熱鍵:
 - `F1` 或是 `Shift + cmd + p` command palette: 呼叫出命令列，可以跑一些 IDE 的指令：比如说安裝 extension．
 - `F2` Rename (`gorename`): 快速的修改名称，
 - F5  Debug :debug 模式．可以以 `hot-key` 的方式 debug :
   - `F5` continue
   - `F10` step over
   - `F11` step into
   - `SHIFT+F11` step out (目前 [delve](https://github.com/derekparker/delve/issues/358) 尚未支持)
   - `SHIFT+F5` stop
   - `F8` Go to Next Error or Warning: 可以快速切到如果有任何 error 或是 warning 的部分． 要切切换到前一个，可以试 (`Shift + F8`)
 - `F12` Go to Definition (godef):跳到 function 定义的地方．
 - `Shift+F12` Show References (guru （也就是 的 oracle) ):
 - 跳到參考的地方，跟 F12 是成对的． 当你跳到 function 定义的地方，可以透过这个跳回呼叫 function 的地方．
 - `Cmd + =` 縮放页面：
 - Zoom in : Cmd + =, Zoom out: Cmd + -(Cmd + Shift + n) 打开新的 IDE
 - `ctrl+alt+p / F1` 万能键
 - `F12` 调到声明的地方
 - `ctrl+alt+f` 全文搜索
 - `ctrl+p` 快速切换文件
 - `ctrl+点击指定文件`，将此文件在分屏栏打开
 - `ctrl+d` 多选
 - ``


## golang.org/x 包出错不用代理的解决办法
由于国内的网络环境导致在下载依赖 golang.org/x 下的包时一般会出错。
比如 :

https fetch failed: Get https://golang.org/x/tools/go/buildutil?go-get=1: dial tcp 216.239.37.1:443: i/o timeout

一般你可以通过设置代理，如果代理使用了 pac 模式的话，可能需要编辑下 pac 文件。下面要介绍的是一种不代理的解决办法。
原理

当我们使用 import "golang.org/x/tools/go/buildutil" 导入包时，其实导入的是$GOPATH/src/golang.org/x/tools/go/buildutil目录的包。
"golang.org/x" 下的包在 "github.com/golang"有镜像库。
所以我们可以从 github.com 上将对应包下载下来放到对应的目录即可。

实例
比如先切换到 $GOPATH 的 src 目录，cd $GOPATH/src，然后按需要下载：
```bash
git clone --depth 1 https://github.com/golang/tools.git golang.org/x/tools
git clone --depth 1 https://github.com/golang/lint.git golang.org/x/lint
git clone --depth 1 https://github.com/golang/net.git golang.org/x/net
git clone --depth 1 https://github.com/golang/sys.git golang.org/x/sys
git clone --depth 1 https://github.com/golang/crypto.git golang.org/x/crypto
git clone --depth 1 https://github.com/golang/text.git golang.org/x/text
git clone --depth 1 https://github.com/golang/image.git golang.org/x/image
git clone --depth 1 https://github.com/golang/oauth2.git golang.org/x/oauth2
git clone --depth 1 https://github.com/golang/time.git golang.org/x/time
```
参考：

 - [http://www.evanlin.com/dive-with-vscode-golang/]()

 - [http://www.wonsikin.me/2016/06/06/VS-Code-%E6%90%AD%E5%BB%BA-Go-%E5%BC%80%E5%8F%91IDE/]
(http://www.wonsikin.me/2016/06/06/VS-Code-%E6%90%AD%E5%BB%BA-Go-%E5%BC%80%E5%8F%91IDE/)

 - [http://kumadocs.com/blog/?p=674](http://kumadocs.com/blog/?p=674)

  [1]: https://imgs.gnux.cn/usr/uploads/2016/11/3942577838.png
  [2]: https://imgs.gnux.cn/usr/uploads/2016/11/498454276.png
  [3]: https://imgs.gnux.cn/usr/uploads/2016/11/56273686.png
  [4]: https://imgs.gnux.cn/usr/uploads/2016/11/1546711108.png
  [5]: https://imgs.gnux.cn/usr/uploads/2016/11/3464256057.png