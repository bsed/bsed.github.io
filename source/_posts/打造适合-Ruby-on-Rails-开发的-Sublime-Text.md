---
title: "打造适合 Ruby on Rails 开发的 Sublime Text"
categories: [ "Rails" ]
tags: [ "rails","Sublime Text3" ]
draft: false
slug: "build-for-ruby-rails-development-sublime-text-on"
date: "2016-03-26 09:11:00"
---

March 12, 2016 8:46 PM
## 缘起
从零基础开始学习 Ruby / Rails 开始，选了 Vim 作为编辑器，之间的学习曲线非常之陡峭。由于是一直在业余时间学习，差不多花了两年时间，才慢慢习惯了在 Terminal 中使用 Vim, 但一直没有达到熟练使用的程度。慢慢的，我发现有下面几个痛点，我决定转向 Sublime Text.

代码过长后，预览无法预览所有的代码，ST 的预览小窗口可以给你实时的鼓励： 哇！ 我写了这么多行代码了，感觉很爽！
在 Terminal 中打开 vim 后， 复制/粘贴代码，总是有格式问题，很烦人。
MacVim 是一个很好 GUI 客户端，但是在我的电脑上每次换行就自动切换为中文输入，非常烦人，可能上哪里配置有问题，但是我不想再花精力去解决了。
Vim 很强大，但是它是一把没有抛光的上古宝剑，需要耐心的打磨和练习,才能最大发挥它的功能。

##  安装

直接在官网下载安装即可，如果准备长期使用，请购买 License, 支持正版。

## 配置
强大的 Package Control
Package Control 是一个包管理工具，类似于 `Homebrew, NPM`, 用它来管理所有 ST 插件非常方便

- 安装：[https://packagecontrol.io/installation](https://packagecontrol.io/installation)
- 使用：通过快捷键 `ctrl+shift+p` 打开 `Package Control`, 然后输入 `Package Install` 可以搜索 插件并安装， 这是文档

## 基本的配置

打开 Preferences -> Settings-User，就可以为 Sublime Text 做一些基本的定制了
```xml
{
    "auto_complete_commit_on_tab": true,
     // 通过 tab 完成自动补全
    "color_scheme": "Packages/RailsCasts Colour Scheme/RailsCastsColorScheme.tmTheme", 
    // 通过 Package Control 安装的 RailsCasts Theme
    "font_face": "Monaco",
    // 指定字体
    "font_size": 12,
    // 指定字体大小
    "ignored_packages":
    [
        "Vintage"
    ],  
    // 如果安装了 Vintageous, 必须禁用 Vintage 
    "spell_check": true,
    // 拼写检查
    "tab_size": 2, 
    // Ruby 的风格，缩进为 2 
    "translate_tabs_to_space": false, 
    // 使用 space 来表示缩进                           
}
```language
```
关于使用 Tab 和 Space 来表示缩进的区别，可以参考这篇文章：[Changing Between Spaces and Tabs in Sublime Text](https://css-tricks.com/changing-spaces-tabs-sublime-text/)

## 必备插件

 [AdvanceNewFile](https://github.com/skuroda/Sublime-AdvancedNewFile)，此插件非常方便的新建文件和文件夹, 在 Mac 系统中，新建文件的快捷方式为: `command+alt+n`, 新建文件夹的快捷方式为：`shift+command+alt_n`

[Color Picker](https://github.com/weslly/ColorPicker) 在 Sublime Text 中直接调用 Mac 的颜色选择器，这也是我对 Sublime Text 中毒的原因之一。快捷键为：`command + shift + c`

[Color Highlighter](https://github.com/Monnoroch/ColorHighlighter) 可以将 View 中的表示颜色的16进制的值显示出对应的颜色

[Rails Snippet](https://github.com/tadast/sublime-rails-snippets)  这个需要添加四有仓库

[Ruby Test](https://github.com/maltize/sublime-text-2-ruby-tests) ，`contrl+.` : Switching between code and test (create a file if not found)

## Sublime Text 常用快捷键

- `Command + T ` 非常强大的文件查找命令：

- `csc`: 支持模糊匹配,`cotroller\sessions_controller.rb` 可以这样简写
- `csc:10`: 在搜索项后面加上冒号，可以指定光标定位到被查找文件的指定行处
- `csc@ini` : 在搜索项后面用 @ 符号，直接定位到文件中的 method, 搜索 method 时也是支持模糊匹配的
- `Command + D` 选中一段文本后，按下 `Commad + D` 后，会自动选中下文中同样内容的文本，然后就可以一起编辑了。
- `Control + Shift + Up/Down` 在多行中选中同一列，然后一起编辑
- `Command + / (Slan)` 注释一行或取消一行注释
- `Command + Shift + D` 复制当前行或者当前被中的内容

多窗口调整布局的快捷键

- `Option + Command + 2` : 2 列窗口布局
- `Shift + Option + Command + 2`: 2 行窗口布局
- `Option + Command + 1`: 回到一个窗口布局
- 
## 参考资料
[http://www.sublimetext.com/docs/3/index.html](http://www.sublimetext.com/docs/3/index.html)
[http://docs.sublimetext.info/en/latest/index.html](http://docs.sublimetext.info/en/latest/index.html])
[]http://thunderboltlabs.com/blog/2013/11/19/efficiency-with-sublime-tex](http://thunderboltlabs.com/blog/2013/11/19/efficiency-with-sublime-text-and-ruby/)

文章来源：忘记了