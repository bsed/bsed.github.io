---
title: 我所使用的vscode 插件
date: 2017-03-07 16:47:00
updated: 2017-03-08 20:06:29
tags: 
- emacs
categories: 
- linux

---
主题  sublime  
图标主题: set-ui

View In Browser 预览页面（ctrl+F1）

vscode-icons 侧栏的图标，方便定位文件

HTML Snippets 支持HTML5的标签提示

JS-CSS-HTML Formatter 格式化

jQuery Code Snippets jquery 自动提示

npm Intellisense npm包代码提示 导入模块时，提示已安装模块名称

ESLint 检测JS必备 ESLint插件，高亮提示

CSS csscomb 格式化

Debugger for Chrome 方便调试

Auto Rename Tag 自动同步修改标签 

Bootstrap 3 Snippets bootstrap必备

preview 预览 markdown， html等

guides 显示代码辅助线

插件列表

Auto Close Tag 自动闭合HTML标签

Auto Rename Tag 修改HTML标签时，自动修改匹配的标签

Bookmarks 添加行书签

Can I Use HTML5、CSS3、SVG的浏览器兼容性检查

Code Runner 运行选中代码段（支持大量语言，包括Node）

CodeBing 在VSCode中弹出浏览器并搜索，可编辑搜索引擎

Color Highlight 颜色值在代码中高亮显示

Color Picker 拾色器

Document This 注释文档生成

EditorConfig for VS Code EditorConfig 插件

Emoji 在代码中输入emoji

File Peek 根据路径字符串，快速定位到文件

Font-awesome codes for html FontAwesome提示代码段

ftp-sync 同步文件到ftp

Git Blame 在状态栏显示当前行的Git信息

Git History(git log) 查看git log

GitLens 显示文件最近的commit和作者，显示当前行commit信息

Guides 高亮缩进基准线

Gulp Snippets Gulp代码段

HTML CSS Class Completion CSS class提示

HTML CSS Support css自动补齐（支持vue）

HTMLHint HTML格式提示

Indenticator 缩进高亮

JavaScript (ES6) code snippets ES6语法代码段

language-stylus Stylus语法高亮和提示

Lodash Lodash代码段

markdownlint Markdown格式提示

MochaSnippets Mocha代码段

Node modules resolve 快速导航到Node模块

npm 运行npm命令

Output Colorizer 彩色输出信息

Partial Diff 对比两段代码或文件

Path Autocomplete 路径自动补齐

Path Intellisense 另一个路径完成提示

Prettify JSON 格式化JSON

Project Manager 快速切换项目

REST Client 发送REST风格的HTTP请求

Settings Sync VSCode设置同步到Gist

String Manipulation 字符串转换处理（驼峰、大写开头、下划线等等）

Test Spec Generator 测试用例生成（支持chai、should、jasmine）

TODO Parser Todo管理

Version Lens package.json文件显示模块当前版本和最新版本

vetur 目前比较好的Vue语法高亮

Vue 2 Snippets vue必备

VueHelper Vue2代码段（包括Vue2 api、vue-router2、vuex2）

View Node Package 快速打开选中模块的主页和代码仓库

VSCode Great Icons 文件图标拓展

Auto-Open Markdown Preview

Character Count

Docker Support

Excel Viewer

FontSize Shortcut

Highlight Trailing White Spaces

Hopscotch

vscode-npm-script

Runner

SCSS IntelliSense

SVG Viewer

附录：VSCode首选项配置

```json
{
    "editor.tabSize": 2,
    "files.associations": {
        "*.vue": "vue"
    },
    "eslint.autoFixOnSave": true,
    "eslint.options": {
        "extensions": [
            ".js",
            ".vue"
        ]
    },
    "eslint.validate": [
        "javascript",
        "javascriptreact",
        "vue",
        "vue-html"
    ],
    "search.exclude": {
        "**/node_modules": true,
        "**/bower_components": true,
        "**/dist": true
    },
    "emmet.syntaxProfiles": {
        "javascript": "jsx",
        "vue": "html",
        "vue-html": "html"
    },
    "extensions.autoUpdate": true,
    "editor.renderWhitespace": "boundary",
    "editor.cursorBlinking": "smooth",
    "workbench.welcome.enabled": true
}
```

参考:
[](https://gold.xitu.io/entry/58ae289e2f301e0068ecf207)

