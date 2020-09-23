---
title: Debugging Rust on Windows using Visual Studio Code
date: 2018-05-28 12:30:00
updated: 2018-05-28 13:01:46
tags: 
- mysql
- import
categories: 
- sql

---
## 首先下载VSCode  

如果没有visual Studio的话，要下载[Visual Studio Build Tool 2015 ](http://landinghub.visualstudio.com/visual-cpp-build-tools) 


<!--more-->


## 其次，启动VS Code，安装Extension

1. ext install C++  -- 安装C++（Windows)
2. ext install Debug  -- 安装GDB，llDB
3. ext install vscode-rust

## 然后，安装ToolChain

### 安装RLS参考：

```
rustup self update

rustup update nightly

rustup component add rls --toolchain nightly

rustup component add rust-analysis --toolchain nightly

rustup component add rust-src --toolchain nightly

rustup component add rust-src --toolchain stable
```
### 添加环境变量

GNU版本
```bash
set RUST_SRC_PATH=%USERPROFILE%\.rustup\toolchains\stable-x86_64-pc-windows-gnu\lib\rustlib\src\rust\src
```
MSVC版本
```bash
set RUST_SRC_PATH=%USERPROFILE%\.rustup\toolchains\nightly-x86_64-pc-windows-msvc\lib\rustlib\src\rust\src
```
### 使用Cargo包管理器安装
```bash
cargo install racer
cargo install rustfmt
cargo install rustsym
```
如图：
![20180528121419.png][1]
## WASM编译： 安装
```bash
rustup target add wasm32-unknown-emscripten nightly
```
在VS Code中点击选择“Add Configuration", 这回自动在 .vscode目录下生成一个`Launch.json`。进行相应的修改，于本地环境相配。
这是我的`Launch.json`
```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "(Windows) Launch",
            
            "type": "cppvsdbg",
            "request": "launch",
            "program": "${workspaceRoot}/target/debug/hello_world.exe",
            "symbolSearchPath": "C:\\Symbols",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceRoot}",
            "environment": [],
            "externalConsole": true,
            "sourceFileMap": {
                "c:/projects":"C:/Users/Administrator/.multirust/toolchains/nightly-x86_64-pc-windows-msvc/lib/rustlib/src"
            }
        }
    ]
}
```

## 然后，编译RUST程序

## Cargo Build
![20180528122912.png][2]

## 点击绿色的"play"按钮
![20180528123016.png][3]

我的 vscode 配置：
```json
{
    "evermonkey.token": "S=s2:U=108b6a9:E=166c8b45e40:C=15f71032f38:P=1cd:A=en-devtoken:V=2:H=e5205536fccf0804da1726ce1eaae864",
    "evermonkey.noteStoreUrl": "https://app.yinxiang.com/shard/s2/notestore",
    "files.eol": "\n",
    "[markdown]": {
        "editor.quickSuggestions": true
    },
    "workbench.colorTheme": "Agila Light Solarized",
    "liveServer.settings.donotShowInfoMsg": true,

    // rust
    "debug.allowBreakpointsEverywhere": true,
    "terminal.integrated.shell.windows": "C:\\WINDOWS\\System32\\cmd.exe",
    "rust.rustup": {
        "toolchain": "nightly-x86_64-pc-windows-msvc",
        "nightlyToolchain": "nightly-x86_64-pc-windows-msvc"
    },
    "rust.mode": "rls",
    "rust.rls": {
        "useRustfmt": true
    },
    "rust-client.channel": "nightly",
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
    "go.gopath": "E:/gocode",
    // Specifies the GOROOT to use when no environment variable is set.
    "go.goroot": "D:/go",
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
    "workbench.iconTheme": "material-icon-theme",
    "explorer.openEditors.visible": 20
}
```
参考：
- [https://www.patriksvensson.se/2018/02/debugging-rust-on-windows-using-vscode](https://www.patriksvensson.se/2018/02/debugging-rust-on-windows-using-vscode)
- [https://www.brycevandyk.com/debug-rust-on-windows-with-visual-studio-code-and-the-msvc-debugger/](https://www.brycevandyk.com/debug-rust-on-windows-with-visual-studio-code-and-the-msvc-debugger/)
- [https://vvl.me/2018/03/31/rust-introduction/](https://vvl.me/2018/03/31/rust-introduction/)
  [1]: https://imgs.gnux.cn/usr/uploads/2018/05/540860606.png
  [2]: https://imgs.gnux.cn/usr/uploads/2018/05/2645104927.png
  [3]: https://imgs.gnux.cn/usr/uploads/2018/05/3209244584.png