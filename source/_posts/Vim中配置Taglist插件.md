---
title: "Vim中配置Taglist插件"
categories: [ "Linux" ]
tags: [ "ctags" ]
draft: false
slug: "the-vim-taglist-plugin-configuration"
date: "2015-05-16 16:40:00"
---

在常用的IDE中，都有这样的一个功能，就是把当前源文件中的类名，方法名等列为一个列表，方便用户阅读源代码，并且可以用这样的列表，在源代码中自由跳转。Vim中其实也可以实现这样的功能，够强大吧？用Vim+Ctags+Taglist就可以搞定。
简单配置如下：

# 安装Ctags#


<!--more-->


如果你的系统中没有Ctags，就安装一个吧，我的是MacOS系统，默认用的Ctags跟这里用的Ctags不是一个版本，默认的ctags只支持少有的几种语言，我需要安装的是Exuberant Ctags。验证的方法，就是在终端中输入 ctags -R，如果提示下面的错误，表明你得再装一个我们需要的ctags：

`ctags: illegal option — R usage: ctags [-BFadtuwvx] [-f tagsfile] file …`

安装也很简单，如果你用homebrew，那就方便多了：

`brew install ctags`

在源代码工程下用ctags -R，就会生成对应的tag文件，到时候taglist是会用上的。

# 安装Taglist #

用Vundle，配置上Taglist的地址，然后自动就安装了。重点是在Taglist的配置上，我的配置选项如下：

"Settings for tagslist

let Tlist_Use_Right_Window = 1 "|让taglist窗口出现在Vim的右边

let Tlist_File_Fold_Auto_Close = 1 "当同时显示多个文件中的tag时，设置为1，可使taglist只显示当前文件tag，其它文件的tag都被折叠起来。

let Tlist_Show_One_File = 1 "只显示一个文件中的tag，默认为显示多个

let Tlist_Sort_Type =‘name’ "Tag的排序规则，以名字排序。默认是以在文件中出现的顺序排序

let Tlist_GainFocus_On_ToggleOpen = 1 "Taglist窗口打开时，立刻切换为有焦点状态

let Tlist_Exit_OnlyWindow = 1 "如果taglist窗口是最后一个窗口，则退出vim

let Tlist_WinWidth = 32 “设置窗体宽度为32，可以根据自己喜好设置

let Tlist_Ctags_Cmd =‘/usr/local/Cellar/ctags/5.8/bin/ctags’ “这里比较重要了，设置ctags的位置，不是指向MacOS自带的那个，而是我们用homebrew安装的那个

map t :TlistToggle “热键设置，我设置成Leader+t来呼出和关闭Taglist

一切设置搞定，保存vimrc文件，退出并重启Vim，用Leader key+t呼出Taglist，效果如下：

![vim2.png][1]


  [1]: https://imgs.gnux.cn/usr/uploads/2015/05/2748061458.png