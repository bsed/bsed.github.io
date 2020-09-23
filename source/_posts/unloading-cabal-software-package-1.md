---
title: 在 Arch Linux 下如何优雅的卸载Cabal软件包
date: 2019-08-15 13:19:00
updated: 2019-08-15 16:08:30
tags: 
- archlinux
- cabal-gc
- cabal
categories: 
- linux

---
本文是对*rufflewind* 编写的[Uninstalling Cabal packages](https://rufflewind.com/2015-02-01/cabal-gc)的翻译。
![19169-m5p7387r7rr.png](https://imgs.gnux.cn/usr/uploads/2019/08/2268019983.png)
## 问题

### A.无法在Cabal中卸载软件包

目前，[cabal-install ](https://haskell.org/cabal)[不知道如何](https://github.com/haskell/cabal/issues/227) [卸载软件包](https://www.reddit.com/r/haskell/comments/lrc39/why_is_there_no_cabal_)。您可以做的最好的事情是使用GHC包数据库管理器[取消注册](https://stackoverflow.com/a/10587933)包，从数据库中删除包元数据而不删除实际文件。


<!--more-->


有一个名为[cabal-uninstall](https://hackage.haskell.org/package/cabal-uninstall)（可能还有其他几个）的软件包在某种程度上可以解决这个问题，但是如果软件包*已经*取消注册，它就不起作用了。

此外，虽然ghc-pkg确实警告您依赖性破坏，但它并没有提供一种简单的方法来传递删除包及其所有依赖项。

### B.删除破损的包裹是一项繁琐的工作

如果由于某种原因你的包损坏了，那么就没有简单的办法来清理这些烂摊子。

在我的系统上，这通常是因为Arch Linux升级了一些全局安装的软件包，在这个过程中破坏了用户安装的软件包（或者可能是由于GHC升级）。

要解决此问题，您必须取消注册所有已损坏的软件包。如果你想要收回坏包所用的空间，你必须`~/.cabal`手动潜入并移除它们。

## 工具

为了帮助解决上述问题，以下是一些协助该过程的脚本：

- [cabal-gc](https://github.com/Rufflewind/config/blob/master/home/sbin/cabal-gc)：一个“垃圾收集器”，用于搜索文件仍然存在但不再注册的任何延迟包。（*注意：它不会删除bin目录中的任何可执行文件。*）
- [ghc-pkg-check](https://github.com/Rufflewind/config/blob/master/home/sbin/ghc-pkg-check)：一个轻量的包装器`ghc-pkg check`可以抑制[haddock warnings](https://ghc.haskell.org/trac/ghc/ticket/8981)，[似乎没有人关心](https://stackoverflow.com/q/22554498)。
- [ghc-pkg-unregister](https://github.com/Rufflewind/config/blob/master/home/sbin/ghc-pkg-unregister)：一个轻量的包装器`ghc-pkg unregister`，允许在一次调用中取消注册多个包。

虽然可以完全自动化这个过程，但最好一次完成一个，以确保不会发生任何不良事件。

## 教程

首先，如果要管理**沙箱中的**包，请务必*在*下面的所有其他内容*之前*运行此命令：

```
cabal [--sandbox-config-file=./cabal.sandbox.config] exec -- "$SHELL"
```

（`--sandbox-config-file=…`标志是可选的。）

现在，

- 如果您的目标是**卸载特定包**及其依赖者，请按照以下所有步骤操作;
- 如果你的目标只是**删除破损的包**，那么直接跳到第2步。

### 1.删除目标包

要删除包，首先要做的是运行：

```bash
ghc-pkg-unregister my-package-0.5.0 some-package-1.0.0 …
```

如果有任何依赖包，您将收到一个错误，告诉您这些包将被破坏：

>
> ghc-pkg: unregistering some-package-1.0.0 would break the following packages: other-package-2.0.0 another-package-3.0.0 (use --force to override)
>



如果您对此没问题，请添加`--force`标记以将其删除。我们很快就会消除破损的包裹。

```bash
ghc-pkg-unregister --force some-package-1.0.0 …
```

> unregistering some-package-1.0.0 would break the following packages: other-package-2.0.0 another-package-3.0.0 (ignoring)
>

尽管消息中存在[条件性情绪](https://en.wikipedia.org/wiki/Uses_of_English_verb_forms#Conditional)，但包裹确实会被取消注册。

### 2.删除依赖包

现在，我们需要摆脱破损的包裹。让我们看看哪些包破了：

```bash
ghc-pkg-check
```
> There are problems in package other-package-2.0.0:
>   dependency "some-package-1.0.0-…" doesn't exist
> There are problems in package another-package-3.0.0:
>   dependency "some-package-1.0.0-…" doesn't exist
>
> The following packages are broken, either because they have a problem
> listed above, or because they depend on a broken package.
> other-package-2.0.0
> another-package-3.0.0


要删除*所有*这些损坏的包，请运行：

```bash
ghc-pkg-check --simple-output | xargs ghc-pkg-unregister --force 
```

为了确保所有损坏的包都得到处理，请`ghc-pkg-check`再次运行。如果有更多破损的包裹，您需要继续重复这一步骤，直到所有这些步骤都被消除。

### 3.删除关联的文件

此步骤是可选的，但回收一些磁盘空间可能很有用。

由于我们将*删除文件*，因此最好先执行干运行：

```bash
cabal-gc
```
> can be removed: /home/user/.cabal/lib/x86_64-linux-ghc-7.10.1/anoth_d9K7jTHCBftCdWJFHesDel
> can be removed: /home/user/.cabal/lib/x86_64-linux-ghc-7.10.1/other_8j0AdSQVkKosS3ev8r3pBu
> can be removed: /home/user/.cabal/lib/x86_64-linux-ghc-7.10.1/mypac_XYf894wKraySpNs9RUwwMV
> can be removed: /home/user/.cabal/lib/x86_64-linux-ghc-7.10.1/somep_x3XTaLFeSmcMHNpv7Ng8xw
> can be removed: /home/user/.cabal/share/doc/x86_64-linux-ghc-7.10.1/another-package-3.0.0
> can be removed: /home/user/.cabal/share/doc/x86_64-linux-ghc-7.10.1/other-package-2.0.0
> can be removed: /home/user/.cabal/share/doc/x86_64-linux-ghc-7.10.1/my-package-0.5.0
> can be removed: /home/user/.cabal/share/doc/x86_64-linux-ghc-7.10.1/some-package-1.0.0

如果一切正常，请继续删除它们：

```
cabal-gc -r
```
> removed: /home/user/.cabal/lib/x86_64-linux-ghc-7.10.1/anoth_d9K7jTHCBftCdWJFHesDel
> removed: /home/user/.cabal/lib/x86_64-linux-ghc-7.10.1/other_8j0AdSQVkKosS3ev8r3pBu
> removed: /home/user/.cabal/lib/x86_64-linux-ghc-7.10.1/mypac_XYf894wKraySpNs9RUwwMV
> removed: /home/user/.cabal/lib/x86_64-linux-ghc-7.10.1/somep_x3XTaLFeSmcMHNpv7Ng8xw
> removed: /home/user/.cabal/share/doc/x86_64-linux-ghc-7.10.1/another-package-3.0.0
> removed: /home/user/.cabal/share/doc/x86_64-linux-ghc-7.10.1/other-package-2.0.0
> removed: /home/user/.cabal/share/doc/x86_64-linux-ghc-7.10.1/my-package-0.5.0
> removed: /home/user/.cabal/share/doc/x86_64-linux-ghc-7.10.1/some-package-1.0.0

笔者使用的 cabal-gc 相关文件 见 [https://github.com/bsed/dotfiles/tree/master/bin](https://github.com/bsed/dotfiles/tree/master/bin)