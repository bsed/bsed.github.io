---
title: window10下 搭建 Rust 环境
date: 2017-11-20 12:53:00
updated: 2019-07-08 13:49:51
tags: 
- css
categories: 
- css

---
Rust是一门强调安全、并发、高效的系统编程语言。无GC实现内存安全机制、无数据竞争的并发机制、无运行时开销的抽象机制，是Rust独特的优越特性。 它声称解决了传统C语言和C++语言几十年来饱受责难的内存安全问题，同时还保持了很高的运行效率、很深的底层控制、很广的应用范围， 在系统编程领域具有强劲的竞争力和广阔的应用前景。

## 搭建Rust开发环境 MSVC 版本

适用于 macOS 和 Windows。

## 安装 Rust 编译环境

之前在 Windows 上，Rust 有独立的安装工具，macOS 上面可以用 Home Brew 安装 Rust。


<!--more-->


但是这样会有些问题，即无法动态切换 Rust 版本，另外源码需要单独配置。

现在官方推荐使用 [rustup](https://rustup.rs/) 来安装 Rust 环境。

### macOS 安装 rustup 

运行下面的脚本安装：

```bash
$ curl https://sh.rustup.rs -sSf | sh
```

注意，不要使用 Home Brew 安装 rustup，会有一些问题。

### Windows 安装 rustup

![27043-80r8r2cvxvk.png](https://imgs.gnux.cn/usr/uploads/2019/04/1223768007.png)
下载 rustup-init.exe，运行之后按照屏幕提示即可。

注意，在 Windows 上面，Rust 编译需要 Visual C++ Build Tools。请先安装这个之后再安装 rustup。

你非要不安装也行，rustup 会给出警告，工具链会被接换到 Gun C 上面，可能会有一些问题。

关于这一部分，请参考 <https://github.com/rust-lang-nursery/rustup.rs#working-with-rust-on-windows>

双击下载的 `rustup-init.exe` 文件，它会先检查 C++ 构建工具，Windows 上会检查是否安装了 Microsoft Visual C++ Build Tools（msvc），没安装会给出下载链接，要求先安装 msvc。

有三个选项：

- 1 安装。
- 2 配置。
- 3 取消。

如果电脑里有 msvc，直接 `1` Enter，如果没有，msvc 需要单独下载安装，而且最小也要将近 1.5 G，所以可以修改为 gnu 安装。(笔者用的windows10 已经装了msvc库)
![rust_install_01.png][1]
默认安装的是 `i686-pc-windows-msvc` 或 `x86_64-pc-windows-msvc`（64 位），通过 `2` Enter 把 target 修改为 `i686-pc-windows-gnu` 或 `x86_64-pc-windows-gnu`(注意笔者截图装的是nightly版的 x86_64-pc-windows-msvc 见图)。(**注意**：20190708 笔者现在 windows、mac、linux 全部切换为 gnu版本了)
![rust_install_02.png][2]
还会有两个问题，一个是安装稳定版还是开发版，一个是自动添加环境变量，都直接 Enter。

修改后会再问一遍上面三个选项，1 Enter 安装。

![rust_install_03.png][3]
####  环境变量

一般不用修改，可以跳过此节。

二进制包默认安装在 %USERPROFILE%\.cargo\bin 中，如果一定要自定义安装位置，安装前配置 RUSTUP_HOME 和 CARGO_HOME环境变量。

| 环境变量               | 默认值                                 | 说明                                       |
| ------------------ | ----------------------------------- | ---------------------------------------- |
| RUSTUP_HOME        | ~/.rustup 或 %USERPROFILE%/.rustup   | rustup 的主目录，保存工具链和配置文件的地方。               |
| CARGO_HOME         | ~/.cargo 或 %USERPROFILE%/.cargo     | Cargo 的主目录。                              |
| RUSTUP_TOOLCHAIN   | none                                | 如果设置了，会覆盖所有 rust 工具调用的工具链，必须是已安装的工具链的名字，否则会调用失败。 |
| RUSTUP_DIST_SERVER | https://static.rust-lang.org        | 设置静态资源的下载源。速度慢可以改为国内镜像。不过鉴于国内环境恶劣[[1\]](http://www.bary.com/doc/a/213888065372979019/#xcode)，最好还是学会翻他一个墙。 |
| RUSTUP_UPDATE_ROOT | https://static.rust-lang.org/rustup | 更新源，参见 RUSTUP_DIST_SERVER。               |

### 更新与卸载

安装 rustup 之后，会默认安装 Rust stable (笔者上面安装的是nightly)。

## 校验Rust编译环境

运行windows命令行(快捷键:Win + R 输入cmd 回车). 输入如下命令并回车

```
> rustc --version
rustc 1.21.0 (3b72af97e 2017-10-09)

> cargo --version
cargo 0.22.0 (3423351a5 2017-10-06)
```

正确输出版本信息说明安装和环境变量配置成功. **假如出现 “‘rustc’不是内部或外部命令，也不是可运行的程序或批处理文件” 请在”环境变量”的Path中增加rust的bin目录**

cargo是rust官方推荐使用的项目管理软件。功能大概类似于java里面的maven和ant。负责管理依赖项和编译。 安装rust时，默认安装(可以自定义为不安装)

如果你需要安装 nightly，运行：

```bash
 rustup install nightly
```

将 nightly 设置为默认 Rust 环境：

```bash
 rustup default nightly
```

更新所有 Rust，运行：

```bash
 rustup update
```

检查 rustup 自身是否有更新：

```bash
 rustup self update
```

但是这一步操作在新版中似乎不需要做了，因为更新 Rust 的时候，貌似也会检查自身是否有更新：

```bash
 rustup update
info: syncing channel updates for 'stable-x86_64-pc-windows-msvc'
info: syncing channel updates for 'nightly-x86_64-pc-windows-msvc'
info: checking for self-updates

   stable-x86_64-pc-windows-msvc unchanged - rustc 1.21.0 (3b72af97e 2017-10-09)
  nightly-x86_64-pc-windows-msvc unchanged - rustc 1.23.0-nightly (5041b3bb3 2017-11-19)
```

卸载 rustup：

```
 rustup self uninstall
```

### 添加 Rust 源代码

rustup 可也以安装配套的源代码，这样你就不需要自己配置了，方法：

```
rustup component add rust-src
```

Rust 所有工具链都被安装在了 `~/.rustup/toolchains/` 目录下面。如果你需要配置就在这里面找。

## 安装其他工具

### 安装 rust-analysis

```bash
rustup component add rust-analysis --toolchain nightly
```

### 安装 rls 
```bash
rustup component add rls --toolchain nightly
```
## 配置 Rust 编辑环境

## 安装VisualRust插件 

VisualRust插件下载地址:

- [MSDN-VisualRust](https://visualstudiogallery.msdn.microsoft.com/c6075d2f-8864-47c0-8333-92f183d3e640/)

下载插件，选择对应的Visual Studio版本安装即可.(**备注**：笔者最近更新了Visual Studio 2019，截止2019/04/18 仓库还没有这个包)

## 安装racer

racer是rust社区里面出现的一个代码补全提示工具。针对多种IDE都有相对应的支持。 (截至于v1.2.6版本貌似只支持标准库的代码提示)

在命令行中输入如下指令并回车:

```
> cargo install racer

```

此方法来自《Rust primer》pdf电子书。博主一直无法编译安装成功。所以采用了下载源码包编译的办法，具体办法如下

- 在<https://github.com/phildawes/racer>项目下载最新的racer包
- 在解压缩之后的目录中，使用cargo编译racer

```
> cd racer
> cargo build --release

```

编译需要联网，cargo会管理和下载racer的相关依赖。中途可能会出现编译失败（基本上都是因为网络原因无法下载到，你懂的）。 多尝试编译几次即可。 编译完成，在racer目录下面的target目录会生成一个racer.exe的可执行文件 将racer.exe文件复制到rust的bin目录下

### 在VS2015中配置racer

点击”工具” -> “选项” -> “Visual Rust” 界面如下:

![vs_visual_rust.jpg][4]
根据racer.exe的路径配置。设置rust源码的source/src的目录。 博主这里设置了RUST_SRC_PATH的环境变量，所以无需手动设置Rust sources的路径

至此，在Windows 10操作系统中使用Visual Studio 2015开发Rust的环境就搭建完成

## cargo设置中文镜像
在 `C:\Users\Administrator\.cargo\config` 文件下输入一下内容。
```
[source.crates-io]
registry = "https://github.com/rust-lang/crates.io-index"
replace-with = 'ustc'
[source.ustc]
registry = "git://mirrors.ustc.edu.cn/crates.io-index"
```
<未完待续>

## windows gnu 版本安装

在Windows平台需要预先安装： Microsoft Visual C++ Build Tools 2015

编译器 与 工具链 安装： 下载安装程序

选择安装提示的第二项（而不是default）－启动交互式安装流程：

I’m going to ask you the value of each these installation options. You may simply press the Enter key to leave unchanged. 
Default host triple? 
x86_64-pc-windows-gnu （解释：为了能够使用gdb进行debug断点，这个是必须的。请不要使用x86_64-pc-windows-msvc分支。它是平台最优，但是gdb不兼容于它。） 
Default toolchain? (stable/beta/nightly) 
stable（我试了nightly版本，有许多的坑，能不使用nightly版，最好先绕过。） 
Modify PATH variable? (y/n) 
y 
工具链包括： 1. rustc 2. cargo 3. rustup

添加Rust的HOME目录 %USERPROFILE%.cargo\bin到操作系统的PATH环境变量里

验证安装成功： rustc –version

Rust IDE

VSCode目前支持Rust开发扩展插件Rust Code与Native Debug

安装RLS参考：
``` 
rustup self update
rustup update nightly
rustup component add rls –-toolchain nightly
rustup component add rust-analysis –-toolchain nightly
rustup component add rust-src –-toolchain nightly
rustup component add rust-src –-toolchain stable
```
添加环境变量

set RUST_SRC_PATH=%USERPROFILE%.rustup\toolchains\stable-x86_64-pc-windows-gnu\lib\rustlib\src\rust\src

使用Cargo包管理器安装

cargo install racer

cargo install rustfmt

cargo install rustsym

WASM编译： 安装

rustup target add wasm32-unknown-emscripten stable

编译

cargo build –target=wasm32-unknown-emscripten

或 完整命令

rustup run stable-x86_64-pc-windows-gnu cargo build –target=wasm32-unknown-emscripten

Debug环境配置参考：

a. 安装gdb64 
追加如下python脚本到D:\Program Files\gdb-7.9.1-tdm64-2\gdb64\bin\gdbinit文件内。 

```python
python
print "-- Loading Rust pretty-printers --"
from os.path import expanduser
sys.path.insert(0, expanduser("~") + "/.rustup/toolchains/stable-x86_64-pc-windows-gnu/lib/rustlib/etc")
import gdb_rust_pretty_printing
gdb_rust_pretty_printing.register_printers(gdb)
 
end
```
ubuntu 下debug 可以查看这篇文章：
[https://tygasoft.com/vscode-debug-rust-project-under-ubuntu.html](https://tygasoft.com/vscode-debug-rust-project-under-ubuntu.html) 或者 这篇 [https://arch.kitekii.pw/2018/12/22/Rust%20Windows%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/](https://arch.kitekii.pw/2018/12/22/Rust%20Windows%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/)


  [1]: https://imgs.gnux.cn/usr/uploads/2019/04/2948790317.png
  [2]: https://imgs.gnux.cn/usr/uploads/2019/04/4070579699.png
  [3]: https://imgs.gnux.cn/usr/uploads/2019/04/4147382539.png
  [4]: https://imgs.gnux.cn/usr/uploads/2017/11/3373410144.jpg

参考：[https://blog.csdn.net/wowotuo/article/details/78441100](https://blog.csdn.net/wowotuo/article/details/78441100)