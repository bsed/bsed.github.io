---
title: 如何在Vscode下搭建Rust 开发环境
date: 2017-06-09 16:09:00
updated: 2019-05-07 17:45:58
tags: 
- golang
- cbc
- blowfish
categories: 
- go

---
！！！此版本太久远了，建议忽略。
我正在使用 [Ms Visual Studio Code](https://code.visualstudio.com/) 开发rust，下面是我的配置

## 使用一个字体

VS Code 已经内置了Rust语法高亮，但是并没有提供一个好的编程字体，我个人比较喜欢`Source Code Pro`

下载并安装使用[Google Fonts](https://www.google.com/fonts#UsePlace:use/Collection:Source+Code+Pro)

在VS Code 中打开菜单首选项 `**Code > Preferences > User Settings**.`, 下面的内容是个人的font配置文件，你可以将字体改成自己喜欢的。


<!--more-->


```
// Place your settings in this file to overwrite the default settings
{
    "editor.fontFamily": "SourceCodePro-Medium",
    "editor.fontSize": 18
}
```
![vscode_font_01.png][1]
保存设置

## 设置 Build Stasks

有了下面的这个配置文件后，你可以使用VS Code 完成编译(`build`)、运行(`run`)、测试(`test`) 等工作。在VS Code 面板中按下 `Ctrl+Shift+P` 打开菜单选项。
![vs.png][2]
输入 `task` 过滤列表
选择 `Tasks: Configure Task Runner` 这一项
在编辑器里输入下面的JSON，配置文件来源于[reddit](https://www.reddit.com/r/rust/comments/389d3o/visual_studio_code_rust_has_been_added_as_a/)

```JSON
{
    "version": "0.1.0",
    "command": "cargo",
    "isShellCommand": true,
    "tasks": [
        {
            "taskName": "build",
            "isBuildCommand": true,
            "showOutput": "always",
            "problemMatcher": {
                "owner": "rust",
                "fileLocation": [
                    "relative",
                    "${workspaceRoot}"
                ],
                "pattern": {
                    "regexp": "^(.*):(\d+):(\d+):\s+(\d+):(\d+)\s+(warning|error):\s+(.*)$",
                    "file": 1,
                    "line": 2,
                    "column": 3,
                    "endLine": 4,
                    "endColumn": 5,
                    "severity": 6,
                    "message": 7
                }
            }
        },
        {
            "taskName": "clean",
            "showOutput": "always"
        },
        {
            "taskName": "run",
            "showOutput": "always"
        },
        {
            "taskName": "test",
            "showOutput": "always",
            "isTestCommand": true,
            "problemMatcher": [
                {
                    "owner": "rust",
                    "fileLocation": [
                        "relative",
                        "${workspaceRoot}"
                    ],
                    "pattern": {
                        "regexp": "^(.*):(\d+):(\d+):\s+(\d+):(\d+)\s+(warning|error):\s+(.*)$",
                        "file": 1,
                        "line": 2,
                        "column": 3,
                        "endLine": 4,
                        "endColumn": 5,
                        "severity": 6,
                        "message": 7
                    }
                },
                {
                    "owner": "rust",
                    "fileLocation": [
                        "relative",
                        "${workspaceRoot}"
                    ],
                    "severity": "error",
                    "pattern": {
                        "regexp": "^.*panicked\s+at\s+'(.*)',\s+(.*):(\d+)$",
                        "message": 1,
                        "file": 2,
                        "line": 3
                    }
                }
            ]
        }
    ]
}
```

## 配置快捷键
VS Code 默认快捷键配置文件中已经有 build (Ctrl+SHIFT+B/Command+Shift+B) 和 test(Ctrl+Shift+t/Command+Shift+t)，但是我们需要设置一个快捷键方便我们在一个可用的任务列表中运行我们上面配置的任何任务。

从VS Code 菜单中选择 `Code > Preferences > Keyboard Shortcuts`, 如下面所示的配置
```json
// Place your key bindings in this file to overwrite the defaults
[
    { "key": "shift+ctrl+r", "command": "workbench.action.tasks.runTask" }
]
```
保存改变。

## 开发项目

我们设置的Task 需要配合Cargo 一起工作。 从VS Code 面板中选择 ` File > Open` 选择Cargo项目的根目录（文件夹包含有Cargo.toml文件），要做构建项目使用ctrl+ Shift + B，编辑器中将高亮显示语法错误和相关警告。
![vscode_rust_03.png][3]
你可以将鼠标移动到带有波浪线的错误处，查看相关错误提示并修改正确，使用 `Ctrl+Shif+t` 测试项目用例，使用 `Ctrl+Shift+r` 打开所有的任务列表。

从任务列表中选择 `run` 运行


  [1]: https://imgs.gnux.cn/usr/uploads/2017/06/2237656131.png
  [2]: https://imgs.gnux.cn/usr/uploads/2017/06/2779172012.png
  [3]: https://imgs.gnux.cn/usr/uploads/2017/06/3369769157.png