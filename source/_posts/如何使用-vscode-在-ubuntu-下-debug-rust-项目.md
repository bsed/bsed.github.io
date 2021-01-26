---
title: "如何使用 vscode 在 ubuntu 下 debug rust 项目"
categories: [ "Linux" ]
tags: [ "vscode" ]
draft: false
slug: "vscode-debug-rust-project-under-ubuntu"
date: "2019-05-01 12:19:00"
---

安装 rust debug插件
```bash
ext install ms-vscode.cpptools
ext install webfreak.debug
ext install vadimcn.vscode-lldb
ext install rust-lang.rust
```
20190709号号修改：
可以将 `ext install rust-lang.rust` 替换为 `ext install swellaby.rust-pack` [官网]


<!--more-->


----------

(https://marketplace.visualstudio.com/items?itemName=swellaby.rust-pack)
[ms-vscode.cpptools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)包：C/C++ 扩展默认是不允许在 Rust 代码上放断点的，安装好扩展之后，打开设置搜索 allowBreakpointsEverywhere，根据自己需求修改全局或者工作区的配置 `"debug.allowBreakpointsEverywhere": true` , 注意：*在 ubuntu 下建议使用代理,不然依赖文件下载不下来*。
[rust-lang.rust](https://marketplace.visualstudio.com/items?itemName=rust-lang.rust)包：rust下的rls服务，不是必须的，但是它内置了cargo build 等tasks 任务,用它提供的 task 省事。
[vadimcn.vscode-lldb](https://github.com/vadimcn/vscode-lldb)包：需要安装lldb包，不同系统安装见 [https://github.com/vadimcn/vscode-lldb](https://github.com/vadimcn/vscode-lldb)

错误一：
Unable to open 'mod.rs': File not found (file:///rustc/fc50f328b0353b285421b8ff5d4100966387a997/src/libcore/fmt/mod.rs).
当出现这种错误时：请看这篇文章：[https://tygasoft.com/solving-rust-project-report-for-debugging-under-vscode.html](https://tygasoft.com/solving-rust-project-report-for-debugging-under-vscode.html)
或者在 `.vscode/launch.json` 下添加 如下配置：(注意改成自己的)
```
"sourceMap": {
                "/rustc/fc50f328b0353b285421b8ff5d4100966387a997/src": "/home/kelvin/.multirust/toolchains/stable-x86_64-unknown-linux-gnu/lib/rustlib/src/rust/src",
            },
```

## debug效果
笔者演示时使用的是lldb, 你也可以配置成lldb-mi, gdb等。
Debug: Linux Dev LLDB
![rust_ubuntu_debug_lldb_01.png][1]
点击 Step Over(F10)
![rust_ubuntu_debug_lldb_02.png][2]
一直点 Step Into(F11)
![rust_ubuntu_debug_lldb_03.png][3]

笔者建议阅读这篇文章配合使用：[https://github.com/yim7/vscode-demo](https://github.com/yim7/vscode-demo)
相关配置：

**vscode settings.json**:
```json
{
    "editor.fontFamily": "'IBM Plex Mono Regular','Droid Sans Mono', 'monospace', monospace, 'Droid Sans Fallback'",
    "editor.fontSize": 18,
    "files.eol": "\n",
    "[markdown]": {
        "editor.quickSuggestions": true
    },
    "workbench.colorTheme": "Quiet Light",
    "liveServer.settings.donotShowInfoMsg": true,
    // go
    // Go configuration
    // Run 'go build'/'go test -c' on save.
    "go.buildOnSave": true,
    // Flags to `go build`/`go test` used during build-on-save or running tests. (e.g. ['-ldflags="-s"'])
    "go.buildFlags": [],
    // Run Lint tool on save.
    "go.lintOnSave": true,
    // Specifies Lint tool name.
    "go.lintTool": "golint",
    // Flags to pass to Lint tool (e.g. ['-min_confidenc=.8'])
    "go.lintFlags": [],
    // Run 'go tool vet' on save.
    "go.vetOnSave": true,
    // Flags to pass to `go tool vet` (e.g. ['-all', '-shadow'])
    "go.vetFlags": [],
    // Pick 'gofmt', 'goimports' or 'goreturns' to run on format.
    "go.formatTool": "goreturns",
    // Flags to pass to format tool (e.g. ['-s'])
    "go.formatFlags": [],
    // Run the formatting tools with the -d flag
    "go.useDiffForFormatting": true,
    // Complete functions with their parameter signature
    "go.useCodeSnippetsOnFunctionSuggest": false,
    // Specifies the GOPATH to use when no environment variable is set.
    "go.gopath": "/home/kelvin/projects/gocode/",
    // Specifies the GOROOT to use when no environment variable is set.
    "go.goroot": "/home/kelvin/go",
    // Run formatting tool on save.
    "go.formatOnSave": true,
    // Run 'go test -coverprofile' on save
    "go.coverOnSave": false,
    // Specifies the timeout for go test in ParseDuration format.
    "go.testTimeout": "30s",
    // Enable gocode's autobuild feature
    "go.gocodeAutoBuild": true,
    // The Go build tags to use for all commands that support a `-tags '...'` argument
    "go.buildTags": "",
    // Environment variables that will passed to the process that runs the Go tests
    "go.testEnvVars": {},
    // Autocomplete members from unimported packages.
    "go.autocompleteUnimportedPackages": true,
    "git.ignoreMissingGitWarning": true,
    "extensions.ignoreRecommendations": false,
    "python.linting.pylintEnabled": true,
    "python.linting.pylintPath": "/home/kelvin/.local/bin/pylint",
    "python.linting.flake8Enabled": true,
    "python.linting.flake8Path": "/home/kelvin/.linuxbrew/bin/flake8",
    "python.linting.enabled": false,
    "editor.rulers": [
        80
    ],
    "files.trimTrailingWhitespace": true,
    //"http.proxy": "socks5://127.0.0.1:1080",
    "http.proxy": "http://127.0.0.1:8118",
    "https.proxy": "http://127.0.0.1:8118",
    "http.proxyStrictSSL": false,
    "workbench.sideBar.location":"right",
    "rust.build_lib": true,
    "rust-client.channel": "stable-x86_64-unknown-linux-gnu",
    "rust-client.rlsPath": "/home/kelvin/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/lib/rustlib/src/rust/src",
    "C_Cpp.updateChannel": "Insiders",
    "debug.allowBreakpointsEverywhere": true
}
```

**.vscode/launch.json**:

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "gdb",
            "request": "launch",
            "name": "Linux Prod",
            "preLaunchTask": "cargo build",
            "target": "./target/debug/${workspaceFolderBasename}",
            "cwd": "${workspaceRoot}"
        },
        {
            "type": "gdb",
            "request": "launch",
            "name": "Linux Dev",
            "preLaunchTask": "cargo build ws",
            "target": "./target/debug/${workspaceFolderBasename}",
            "cwd": "${workspaceRoot}"
        },
        {
            "type": "lldb",
            "request": "launch",
            "name": "Linux Dev Test LLDB",
            "program": "${workspaceRoot}/target/debug/deps/${workspaceFolderBasename}",
            "args": [
                "--nocapture"
            ],
            "cwd": "${workspaceRoot}",
            "sourceLanguages": [
                "rust"
            ],
            "preLaunchTask": "cargo build test",
            "internalConsoleOptions": "openOnSessionStart"
        },
        {
            "type": "lldb",
            "request": "launch",
            "name": "Linux Dev LLDB",
            "program": "${workspaceRoot}/target/debug/${workspaceFolderBasename}",
            "args": [],
            "cwd": "${workspaceRoot}",
            "sourceLanguages": [
                "rust"
            ],
            "preLaunchTask": "cargo build",
            "internalConsoleOptions": "openOnSessionStart",
            "sourceFileMap": {
                "/rustc/fc50f328b0353b285421b8ff5d4100966387a997/src": "/home/kelvin/.multirust/toolchains/stable-x86_64-unknown-linux-gnu/lib/rustlib/src/rust/src",
            },
        },
        {
            "name": "Windows Prod",
            "type": "cppvsdbg",
            "request": "launch",
            "preLaunchTask": "cargo build",
            "program": "target/debug/${workspaceFolderBasename}",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": true,
            "logging": {
                "moduleLoad": false
            }
        },
        {
            "name": "Windows Dev",
            "type": "cppvsdbg",
            "request": "launch",
            "preLaunchTask": "cargo build ws",
            "program": "target/debug/${workspaceFolderBasename}",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": true,
            "logging": {
                "moduleLoad": false
            }
        }
    ]
}
```
**.vscode/tasks.json**：
```
{
    // See https://go.microsoft.com/fwlink/?LinkId=733558
    // for the documentation about the tasks.json format
    "version": "2.0.0",
    "tasks": [
        {
            "type": "shell",
            "label": "cargo test build",
            "command": "cargo",
            "args": [
                "test",
                "--no-run"
            ],
            "problemMatcher": [
                "$rustc"
            ]
        },
        {
            "type": "shell",
            "label": "cargo build",
            "command": "cargo",
            "args": [
                "build"
            ],
            "problemMatcher": [
                "$rustc"
            ],
            "presentation": {
                "echo": true,
                "reveal": "silent",
                "focus": false,
                "panel": "shared",
                "showReuseMessage": true,
                "clear": true
            },
            "group": {
                "kind": "build",
                "isDefault": true
            }
        },
        {
            "type": "shell",
            "label": "cargo build ws",
            "command": "cargo",
            "args": [
                "build",
            ],
            "problemMatcher": [
                "$rustc"
            ],
            "group": {
                "kind": "build",
                "isDefault": true
            }
        },
        {
            "type": "shell",
            "label": "cargo doc",
            "command": "cargo doc --open",
            "problemMatcher": [
                "$rustc"
            ]
        },
        {
            "type": "npm",
            "script": "release",
            "path": "ui/",
            "group": {
                "kind": "test",
                "isDefault": true
            }
        },
        {
            "type": "npm",
            "script": "releaseall",
            "path": "ui/",
            "problemMatcher": []
        }
    ]
}
```

参考：

 - [http://kotoyaku.info/programing/rust/20181226-debug](http://kotoyaku.info/programing/rust/20181226-debug)
 - [https://github.com/yim7/vscode-demo](https://github.com/yim7/vscode-demo)
 - [https://ryochack.hatenablog.com/entry/2018/02/12/231755](https://ryochack.hatenablog.com/entry/2018/02/12/231755)
  [1]: https://imgs.gnux.cn/usr/uploads/2019/05/1289388741.png
  [2]: https://imgs.gnux.cn/usr/uploads/2019/05/2962914652.png
  [3]: https://imgs.gnux.cn/usr/uploads/2019/05/651404907.png