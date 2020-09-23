---
title: Mac pyenv install 3.7.2 The Python ssl extension was not compiled. Missing the OpenSSL lib
date: 2020-05-02 18:22:00
updated: 2020-07-10 14:08:32
tags: 
- mac
- brew
categories: 
- os

---
Python非常适合编写AWS Lambda函数。对于本地开发，您可能希望能够在Mac上运行所有受支持的[Python 运行时](https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtimes.html)。不幸的是，设置起来并不像您想的那么简单。

管理各种版本的Python的安装的一种好方法是使用pyenv。尽管安装pyenv本身很简单，但以后可能会遇到一些麻烦。这篇文章并不是要介绍pyenv，而是要解决以下问题的指南：

 - Python版本无法编译
 - pyenv托管的Python与Homebrew搭配使用效果不佳
 - 自制软件托管的Python环境会干扰pyenv


<!--more-->


这篇文章显示了如何配置系统以避免这些问题。

## 设置pyenv
只需使用Homebrew安装pyenv并对Shell的配置进行少量更改就足以使pyenv本身正常工作。（不要忘记在更改其配置后重新加载您的shell,一般使用 `source ～/.zshrc` ）

Shell	Config snippet	Config file
bash	eval (pyenv init -)	~/.bash_profile or ~/.bashrc
zsh	eval (pyenv init -)	~/.zshenv
fish	source (pyenv init – |psub)	~/.config/fish/config.fish

但是，如果您现在尝试安装Python版本，则很可能会失败。首先，您需要确保已安装用于构建Python的依赖项。

技巧1：安装构建依赖项
在Mac上编译Python需要一些开发标头。
```bash
sudo installer -pkg /Library/Developer/CommandLineTools/Packages/macOS_SDK_headers_for_macOS_10.14.pkg -target /
```
安装macOS更新后，您将需要（重新）安装这些标头！参见：[https : //github.com/pyenv/pyenv/wiki](https : //github.com/pyenv/pyenv/wiki)

尽管不是必需的，但pyenv Wiki建议安装一些其他库。

```bash
brew install openssl readline sqlite3 xz zlib
```
现在，您尝试通过执行来安装所需的任何python版本 `pyenv install $VERSION`。有关可用版本的列表，请执行`pyenv install --list`。
```bash
pyenv install 3.7.2
pyenv install 3.6.8
pyenv install 2.7.15
```
最后，将pyenv配置为默认使用这些Python版本之一。

pyenv global 3.7.2
让pyenv和Homebrew一起玩
如果您运行brew doctor，则可能会有一些抱怨。
```bash
$ brew doctor
Please note that these warnings are just used to help the Homebrew maintainers
with debugging if you file an issue. If everything you use Homebrew for is
working fine: please don't worry or file an issue; just ignore this. Thanks!

Warning: "config" scripts exist outside your system or Homebrew directories.
`./configure` scripts often look for *-config scripts to determine if
software packages are installed, and what additional flags to use when
compiling and linking.

Having additional scripts in your path can confuse software installed via
Homebrew if the config script overrides a system or Homebrew provided
script of the same name. We found the following "config" scripts:
~/.pyenv/shims/python3.6m-config
~/.pyenv/shims/python3.7-config
~/.pyenv/shims/python3.7m-config
~/.pyenv/shims/python-config
~/.pyenv/shims/python3-config
~/.pyenv/shims/python3.6-config
```
如果您不解决此问题，则可能会根据pyenv管理的Python版本编译Homebrew的任何Python依赖包。请参阅第二个技巧来创建包装器脚本以解决这些问题。

## 技巧2：酿造包装纸
为brew具有以下内容的可执行文件创建以下包装脚本。
```bash
#!/bin/sh
# check if pyenv is available
# edit: fixed redirect issue in earlier version
if which pyenv >/dev/null 2>&1; then
  # assumes default location of brew in `/usr/local/bin/brew`
  /usr/bin/env PATH="${PATH//$(pyenv root)\/shims:/}" /usr/local/bin/brew "$@"
else
  /usr/local/bin/brew "$@"
fi
```
将它（也称为）存储brew在$ PATH中比真实Brew可执行文件具有更高优先级的位置，以便执行此包装脚本而不是真实Brew。（不要忘记使包装脚本成为可执行文件！）请参阅：[https://github.com/pyenv/pyenv/issues/106](https://github.com/pyenv/pyenv/issues/106)

`brew doctor`与此包装一起运行，很满意。
```bash
$ brew doctor
Your system is ready to brew.
```
## 让brew与pyenv一起玩

pyenv管理的所有Python都很好地藏在某个隐藏的地方，但是Homebrew管理的Python版本（包括pip）中安装的所有内容都放在`/usr/local/bin`。该路径可能会优先于pyenv管理的垫片，从而破坏您的体验。

不幸的是，有些软件包依赖它，因此不能简单地卸载Homebrew管理的Python版本。您可以采取以下措施来防止出现任何问题：

卸载由Homebrew托管的Python版本安装的所有软件包，请参阅第三个技巧

确保pyenv垫片的优先级高于`/usr/local/bin`您的$PATH
## 技巧3：从Homebrew托管的Python版本中卸载pip软件包
```bash
/usr/local/bin/pip2 freeze |xargs /usr/local/bin/pip2 uninstall -y
/usr/local/bin/pip3 freeze |xargs /usr/local/bin/pip3 uninstall -y
```
重复这些命令，直到所有软件包都被删除。

包起来
写这篇文章是为了帮助您解决pyenv的一些问题，它并不意味着如何使用它。有关用户指南，请参阅自述文件。

开心的Pythonning！

[https://binx.io/blog/2019/04/12/installing-pyenv-on-macos/](https://binx.io/blog/2019/04/12/installing-pyenv-on-macos/)