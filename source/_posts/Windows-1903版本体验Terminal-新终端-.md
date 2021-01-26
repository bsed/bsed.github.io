---
title: "Windows 1903版本体验Terminal 新终端 "
categories: [ "日常" ]
tags: [ "windows","terminal" ]
draft: false
slug: "windows-1903-experience-terminal-new-terminal"
date: "2019-05-08 11:56:00"
---

## Windows Terminal 是什么？
Windows Terminal是微软推出的新的命令行应用程序，提供多标签、分割窗口、快捷键、完整的Unicode字符支持等功能。

最重要的是，它支持PowerShell，Cmd，WSL（Windows的Linux子系统）和SSH等命令行程序，可以说是全平台制霸，简化开发者的工作流程。
![windows_terminal_preivew.gif][1]

6不6. ^.^


<!--more-->


Windows Terminal使用的是基于`DirectWrite/DirectX`的GPU加速文本渲染引擎。

这个新的引擎支持显示PC中存在的文本字符，意味着终端里的汉字、日文不再乱码，而且还能玩emoji表情。

在微软展示的Demo中，你可以为自己的shell程序通过测试加入emoji表情提示。
![window_terminal_preivew_02_emoji.jpg][2]

微软还为它增加了一种新的等宽字，以增强现代外观和感觉。这种字体不仅包括编程连字，而且它也是开源的 地址是：[]()
```bash
nuget restore OpenConsole.sln
msbuild OpenConsole.sln
```
官方给的方法好多同学都编译不通过：

笔者使用的VS2017, 操作系统是 Windows 1903 版本的。
VS2017 需要安装一下模块:
1.1. Desktop Development with C++ (default components);
1.2. Universal Windows Platform development (default components);
Git command-line tool;
Nuget.exe (Can be found in Terminal/dep/nuget).
Win10 1903 (According to [#437](https://github.com/microsoft/Terminal/issues/437)).

从github 上 下载源码：
```
git submodule update --init --recursive;
nuget restore OpenConsole.sln;
```
**编译前提：**系统为windows含1903之后的版本 打开Windows设置-升级和安全-对于开发人员，将应用程序源修改为**开发人员模式**。

用vs2017 打开 `OpenConsole.sln`, 右键配置管理器，设置活动解决方案配置为 `Release`,活动解决方案平台，设置为自己系统的平台 `X86/X64` 默认项目又好多`ARM64`架构的导致编译错误:
1. 先清理解决方案
2. CascadiaPackage 设置为启动项目
3. 编译Release/Debug版本
4. 编译成功无错误后，右键该项目部署。
5. 然后你点击"开始" 菜单，发现 有 Windows Terminal (Preview)。

## 错误解决：
- [Guide for build new Terminal #489](https://github.com/microsoft/Terminal/issues/489)
- [How Do I Build it to Begin With? #461](https://github.com/microsoft/Terminal/issues/461)

最终效果如图： 笔者等WLS2出来再搞子系统
从这里找到安装的 Windows Terminal Preview 版 点击进入。
![windows_terminal_preiview_02.png][3]
打开后默认效果如上图所示，可以尝试按`ctrl+t` 切换新界面。
![windows_terminal_preview_03.png][4]
可以切换到图一的多标签，多启动方式模式，默认是cmd和powershell, 如果装上子系统，也会出现子系统信息。
![windows_terminal_preivew_04.png][5]

20160624日更新
已经可以在 Windows 商店下载了。 搜索 `Windows Terminal (Preview)` 如图：
![38458-4rcj7hxzr34.png](https://imgs.gnux.cn/usr/uploads/2019/06/3051391989.png)

  [1]: https://imgs.gnux.cn/usr/uploads/2019/05/1191897638.gif
  [2]: https://imgs.gnux.cn/usr/uploads/2019/05/4132940749.jpg
  [3]: https://imgs.gnux.cn/usr/uploads/2019/05/92362190.png
  [4]: https://imgs.gnux.cn/usr/uploads/2019/05/694744988.png
  [5]: https://imgs.gnux.cn/usr/uploads/2019/05/2654834832.png