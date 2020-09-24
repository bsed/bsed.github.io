---
title: Atom 编辑器 上 10个必备的 插件
date: 2016-02-28 17:16:00
updated: 2016-02-28 18:52:10
tags: 
- web
- frontend
categories: 
- css

---
> GitHub出品的 Atom 是近年来迅速崛起的，最好有的一款代码编辑器之一，插件的数量已成倍增加，在写作时有超过750个主题和2400个包。如果你是一个 nodejs 、web 开发者 Atom是一个很好的选择。

Atom上插件安装是很方便的，你可以通过一下两种方法安装插件。

 1. 打开“设置（setting）”选项卡中的“+ install ”安装面板，然后按名称搜索“包”，
 2. 使用命令行安装APM <包名称>安装 ` apm install <package-name>`。

## 1. Seti UI and Themes

IED编辑器主题一般选择默认的就可以了，我也很少推荐其他主题；但是， Seti UI 插件 使Atom 看上去更加华丽，漂亮，有很多漂亮的icons，包括
SCSS, LESS, JSON, grunt 配置, gulp 配置等等， Monokai 主题 配合 这个 Seti UI 插件  是一个不错的选择，值得一试。
 ![atom_plugins_setui.png][1]

[https://atom.io/themes/seti-ui](https://atom.io/themes/seti-ui)
[https://atom.io/themes/monokai-seti](https://atom.io/themes/monokai-seti)

## 2. Open Recent

和 Sublime 一样， Atom 采用简单的文件夹为基础的项目管理。使用这个插件包 很容易跳到最近使用的文件夹或文件。
![atom_plugins_open-recent.png][2]

[https://atom.io/packages/open-recent](https://atom.io/packages/open-recent)

如果你需要一些更复杂的管理，你可以尝试Project Manager 这个插件：

[https://atom.io/packages/project-manager](https://atom.io/packages/project-manager)

## 3. TODO-show

如果你已经开始使用 Atom， 打开一个文件下，`TODO-show` 可以给你的工程添加一些注释关键字。例如，TODO, FIXME 和 CHANGED,当让了，你也可以添加自己的注视。

![atom_plugins-todo-show.png][3]

[https://atom.io/packages/todo-show](https://atom.io/packages/todo-show)

## 4. Minimap

`Minimap` 是 Atom 中最流行的一个插件，在你的代码的右侧显示一个代码缩略图，听过点击它快速定位到相应的代码上。

![atom_plugins-minimap.png][4]

[https://atom.io/packages/minimap](https://atom.io/packages/minimap)

## 5. Highlight Selected

如果你使用过Sublime或 Notepad++ 的时候，当你搜索一个关键字或者一个变量，搜索到的内容会高亮显示。`Highlight Selected` 插件 将这种功能引入到 Atom, 在配合 `minimap-highlight-selected` 插件，效果会更好。

![atom_plugins_highlight-selected.png][5]

[https://atom.io/packages/highlight-selected](https://atom.io/packages/highlight-selected)
[https://atom.io/packages/minimap-highlight-selected](https://atom.io/packages/minimap-highlight-selected])

## 6. Auto-close HTML

 `Auto-closing HTML`一个很简单的插件，但是我们离不开它，它可以提高你html 编写的效率，这个插件允许你自定义 html Tag 标记 例如:(`<p></p>` 或 `<li></li>`),创建一个新的代码快。

[https://atom.io/packages/autoclose-html](https://atom.io/packages/autoclose-html)

## 7. Pigments

`Pigments` 能够处理css color 十六进制颜色块 例如 (`#fefefe`)，它能够解析颜色，解析预处理变量，甚至是 颜色改变函数。

![atom_plugins-pigments.png][6]

[https://atom.io/packages/pigments](https://atom.io/packages/pigments)

## 8. Linter 

你可以在命令行中运行 linter ，它是低入侵检测代码，发现错误，它也是代码校验工具里最好用的一个。

![atom_plugins-linter.gif][7]

*注意*，`Linter`是核心软件包，它提供一个API的其他辅助插件：数十种语言的支持。例如 `HTML`，`CSS`和`JavaScript` 立即工作，但大部分取决**于特定的引擎或进一步配置安装**

[https://atom.io/packages/linter](https://atom.io/packages/linter)
[https://atom.io/packages/linter-htmlhint](https://atom.io/packages/linter-htmlhint)
[https://atom.io/packages/linter-csslint](https://atom.io/packages/linter-csslint)
[https://atom.io/packages/linter-jshint](https://atom.io/packages/linter-jshint)

## 9. Indentation and Beautification

程序员在编码的时候 决定使用 tab、space 一个字符，两个字符，四个字符，还是8个字符。也有写程序员使用 3个tab 字符例子。 使用  `Auto Detect Indentation` 插件，你再也不用为缩进担忧了，确保每个人的代码格式都一样的话，你也可以使用  `atom-beautify`插件做格式化处理。

[https://atom.io/packages/auto-detect-indentation](https://atom.io/packages/auto-detect-indentation)
[https://atom.io/packages/atom-beautify](https://atom.io/packages/atom-beautify)

## 10. Miscellaneous

`emmet` 就不做解释了。

[https://atom.io/packages/emmet](https://atom.io/packages/emmet)

如果您正在创建`REST` web 服务, `Atom’s REST Client` 提供快速测试工具。这个工具和 chrome 下的 [https://www.getpostman.com/](https://www.getpostman.com/) 类似，值得一试。

![atom_plugins_rest-client.gif][8]

[https://atom.io/packages/rest-client](https://atom.io/packages/rest-client)


  [1]: https://imgs.gnux.cn/usr/uploads/2016/02/4065886326.png
  [2]: https://imgs.gnux.cn/usr/uploads/2016/02/2572504292.png
  [3]: https://imgs.gnux.cn/usr/uploads/2016/02/2290662175.png
  [4]: https://imgs.gnux.cn/usr/uploads/2016/02/3508587607.png
  [5]: https://imgs.gnux.cn/usr/uploads/2016/02/3495425262.png
  [6]: https://imgs.gnux.cn/usr/uploads/2016/02/278990747.png
  [7]: https://imgs.gnux.cn/usr/uploads/2016/02/3192209818.gif
  [8]: https://imgs.gnux.cn/usr/uploads/2016/02/3980969601.gif