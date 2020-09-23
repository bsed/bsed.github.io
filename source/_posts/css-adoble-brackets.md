---
title: brackets：前端开发工程师必备编辑器插件推荐
date: 2014-11-16 10:24:00
updated: 2016-09-11 14:14:37
tags: 
- golang
- gvim
categories: 
- vim

---
### Brackets介绍 ###

> 最近开始使用一个新的编辑器，是Adobe开发的开源的项目，与Sublime
> Text2相比有两点优势：1.可以实时预览；2.可以快速编辑，在HTML可以编辑CSS和JS代码。


<!--more-->


Adobe Brackets是一个开源的基于HTML/CSS/JavaScript开发，运行在native shell上的集成开发环境。该项目由Adobe创建和维护，根据MIT许可证发布。提供Windows和OS X平台支持。

Brackets的特点是简约、快捷，没有很多的视图或者面板，它的核心目标是减少在开发过程中那些效率低下的重复性工作，例如浏览器刷新，修改元素的样式，搜索功能等等。

前去官网下载：[http://brackets.io/](http://brackets.io/)
插件仓库：[https://brackets-registry.aboutweb.com/]
ubuntu(https://brackets-registry.aboutweb.com/) 

源　

    sudo add-apt-repository ppa:webupd8team/brackets
    sudo apt-get update
    sudo apt-get install brackets

卸载命令：

    sudo apt-get remove brackets

移除PPA源：

    sudo apt-get install ppa-purge
    sudo ppa-purge ppa:webupd8team/brackets

###　Brackets使用 ###

官方提供简体中文语言包，在菜单栏处选择debug->language->simple chinese就可更换为简体中文版本
                                        -------^^^^^^^^^--------
左侧目录树顶部会显示你最近打开的正在工作的几个文档，非常的方便。

Brackest会检测文档是否符合html规范，并且还有JS错误提示，点击黄色三角感叹号就会弹出错误提示，如下图
把光标放在一个class或id属性的标签名称上，按下Ctrl/Cmd + E（“编辑”）或退出编辑。Brackets将搜索项目下所有CSS文件，然后打开一个内嵌的编辑器嵌入在HTML文件中，可以让你迅速修改CSS代码。唯一美中不足的是：搜索的是项目下所有的CSS文件，如果能修改成所搜当前文件使用的CSS文件就更好了。
Brackest同样也支持JS的快速预览和编辑。

Brackets内建取色器，提供RGBa、HEX、HSLa的颜色编码形式。把光标放在一个颜色编码上，按下Ctrl/Cmd + E（“编辑”），退出取色器窗口需要使用Esc键。
Brackets提供网页即时预览功能。使用该功能时，Brackets调用Chrome浏览器打开当前页面，此后修改html、css、javascript并保存后，所修改的内容会即时响应到浏览器中的页面，无须手动刷新页面。这是Brackets最大的一个亮点，有两个显示器的coder有福了，可以分屏显示Brackets和chrome，即时修改即时预览，无需切换编辑器/浏览器和刷新页面。

目前即时预览功能的一些限制：
它仅适用于Chrome浏览器为目标浏览器，你必须安装Chrome。
它依赖于在Chrome浏览器中的远程调试功能，这是一个命令行标志启用。在Mac上，如果你已经在使用Chrome浏览器，这时启动“即时预览”，Brackets将询问你是否要重新启动Chrome浏览器启用远程调试功能。
只能同时对一个HTML文件进行预览 – 如果切换另一个HTML文件，Brackets将关闭原来的预览。

### Brackets快捷键 ###

Ctrl/Cmd+Shift+H 可以呼出与关闭文件树

Ctrl/Cmd + E 快速预览/编辑 css样式/javascript函数

 - CSS：光标放在一个class或id属性的标签名称上，按下 `Ctrl+E`。Brackets将搜索项目下所有CSS文件，会打开一个内嵌的编辑器可以让你迅速修改CSS代码。当前 `class/id` 标签有多处样式定义时，编辑窗口提供切换按钮来切换显示样式，也可以使用`Alt - Up/Down` 箭头键切换。需要注意的是，Brackets会检测当前html文档以及项目下所有CSS文件来查找class/id样式，即使某些CSS文件在当前html文档中未被引用到。
 - JS函数：把光标放在一个js函数名称上，按下`Ctrl+ E`，也可以快速预览/编辑。

Ctrl/Cmd + +/- 放大缩小编辑区字体大小

Ctrl/Cmd + 0 重置编辑区字体大小

Ctrl/Cmd + Alt + P 打开即时预览功能

Ctrl/Cmd + / 行注释

Ctrl/Cmd + Alt + / 块注释

**注意**：css代码、html代码注释时只能使用块注释快捷键

Ctrl+W 关闭当前打开的文件

Ctrl+Shift + W 关闭全部打开的文件

Ctrl + L 选中当前行

Ctrl+] 增加行缩进

Ctrl+[ 减少行缩进

Ctrl+Shift + D 删除当前行

Ctrl+空格 显示代码提示

Ctrl+Shift +空格 显示参数提示

Ctrl+Shift+O 打开快速导航

Ctrl +G 转到某行

Ctrl +T 转到定义

Ctrl +J 转到源代码定义处

Ctrl + E 快速预览/编辑 css样式/javascript函数

**注意**:
Brackets有时候会死掉，无法从界面退出，需要`ps -ef | grep Bracket*`查出相关进程，然后使用 `sudo kill -9` 进程号（UBUNTU）。


不足：不支持代码折叠。

Brackets插件推荐

1.代码格式化插件：Beautify

直接打开Brackets 插件管理器，搜索安装beautify。
安装完毕后，选择菜单上的编辑→beautify（或使用快捷键ctrl+alt+L）完成代码格式化

2.HTML代码编辑：Emmet 打代码飞起     http://emmet.io/

最强大的HTML和CSS快速编辑插件

3.CSS和JS压缩插件：Minify

很强大的插件，集成了CSS和JS的压缩，会在同个目录下自动生成*.min.*格式的文件。

4.CSS3代码自动补全插件：auto prefix

使用CanIUse上的css3数据，自动补全css3前缀和浏览器兼容，不过可能有些新了。有一些旧版本的浏览器会被抛弃掉。

5.代码折叠插件：brackets-code-folding

安装URL：https://github.com/thehogfather/brackets-code-folding

默认不支持代码的可折叠功能。通过此扩展的安装，对代码添加可折叠功能。

6.当关闭编辑器时自动保存所有打开未保存的文件：Autosave Files on Window Blur

类似Sublime Text2、phpstorm / webstorm的风格，之前提到的不足全部解决了。

7.Brackets主题：theme  (可垃圾了)

安装URL：https://github.com/MiguelCastillo/Brackets-Themes

8.显示你打开文件对应的图标

![20141102080039_379.png][1]
安装URL：https://github.com/ivogabe/Brackets-Icons

9.javascript 全局变量高亮 

安装URL：https://github.com/ForbesLindesay/brackets-globals

10.javascript 代码审查，建议所写的代码都检查一下   

安装URL：https://github.com/markmcintyre/brackets-jslint

11.更改代理，Brackets安装扩展是被墙了，如果用gae，那么这个插件将会很好用    

安装URL：https://github.com/ohnnyj/brackets-proxy

12.为你的编辑器增加工作空间管理

安装URL： https://github.com/thomasvalera/Brackets-Workspaces

13.代码格式化   

安装URL： https://github.com/drewhamlett/brackets-beautify

14.代码备份

安装URL： https://github.com/joemakev/brackets-backup

15.Brackets Kill Ring  类似Emacs的kill ring

16.Align It  对齐

17.Pretty Json  格式化Json

18.Python flake8 Lint Python的格式化

19.imageToData  图片转为base64字符串

20.Goto Last Edit-- 快速定位到最后编辑位置

21.Lintyai -- 语法提示，

22.W3CValidation-- w3c语法检查，需要联网

23.wordHint  --关键字提示

24.Workspaces --工程管理
该扩展为一个用于使用基于CodeMirror的主题集的扩展。安装该扩展后应用程序菜单中出现“Themes”菜单，开发者可以为应用程序界面从中选用一个主题。

25.Ionic Framework Code Hinting --ionic开发

26.stowball/brackets-legibility -- https://github.com/stowball/brackets-legibility
brackets在linux下，菜单字体有点小，不清晰,装完立马变大

其他的等待发现中，如果你发现有优秀的插件，不妨给我推荐一下。  

    Emmet插件：https://github.com/emmetio/brackets-emmet
        
    AngularJS插件：https://github.com/angular-ui/AngularJS-brackets
        
    Git插件：https://github.com/zaggino/brackets-git
        
    Theme插件：https://github.com/MiguelCastillo/Brackets-Themes
        
    Minifier插件：https://github.com/wylst/brackets-minifier
        
    Beautify插件：https://github.com/drewhjava/brackets-beautify
    
    代码折叠插件: https://github.com/thehogfather/brackets-code-folding
    
    LESS自动编译插件：https://github.com/jdiehl/brackets-less-autocompile

    KnockoutJS-brackets: https://github.com/EricSmekens/KnockoutJS-brackets

***注意：*** 有些插件切记在中文下不可用 ^.^
文章部分参考这里：http://www.zjgsq.com/903.html

**更新**：2015.1
snippets https://github.com/chuyik/brackets-snippets
![](https://camo.githubusercontent.com/be3db0c938d11c2c95d1e7f92d01e432f703d2c4/687474703a2f2f6564776172646368752e6f72672f6173736574732f696d616765732f627261636b6574732d736e6970706574732d64656d6f2e676966)
弹出右键菜单：https://github.com/Denisov21/Popup-menu-extension
![弹出右键菜单](https://github.com/Denisov21/Popup-menu-extension/raw/master/screenshot/Image1.png)

ember.js https://github.com/apezel/brackets-ember-htmlbars
尺子：https://github.com/lkcampbell/brackets-ruler
diff: https://github.com/bomsy/brackets-compare

★PHPLint  

★PHP Syntex Hint 

★QuickDocsPHP--ctrl + k 可以查看文档说明

---以上三个是对php开发有用的插件，最后一个利用ctrl+k查看函数的说明和格式

-----

★autosave files on widow blur -- 这个在关闭时自动保存文件

★beautify--这个对代码进行美化，对html支持很好，想对js送货 得安装jsbeautify，

有一点对php一直没有找到一个好一点的扩展插件来美化php代码

★Console  --这个插件可以在主窗口下面打一个小窗口，提示一些错误信息，我感觉有用，就安装了，大家可以测试一下。一会在后面不回几个图片。

★Goto Last Edit-- 快速定位到最后编辑位置

★Lintyai -- 语法提示，


★W3CValidation-- w3c语法检查，需要联网

★wordHint  --关键字提示

★Workspaces --工程管理

★Open Extensions Folder. --打开扩展插件文件夹（这个可以不用安装，在帮助菜单里有这个功能，但不太一样）

★brackets-tab-tags--OK 
eg: `<p>|</p>` <enter>      

变成
```html
<p>
    |
</p>
```
★brackets icons --图标，可以区分是那种文件html/php/js/css 等

★ vimbar --启动vim操作模式

★code folder --代码折叠插件

★main windows -- 主窗体提醒插件。
Brackets CSS Class Code hint 
JS 帮助文档 - QuickDocsJS 选中位置，使用快捷键 `command + K`（mac），`ctrl + K` （windows），可以快速查看 JavaScript 的帮助文档。

**参考**：

 - http://ninghao.net/blog/1405
 - https://segmentfault.com/a/1190000004113546

  
[1]: https://imgs.gnux.cn/usr/uploads/2014/11/3692043661.png

