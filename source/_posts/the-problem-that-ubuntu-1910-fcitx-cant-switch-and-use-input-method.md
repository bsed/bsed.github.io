---
title: 解决ubuntu19.10 fcitx不能切换和使用输入法的问题
date: 2020-02-25 14:02:00
updated: 2020-03-24 18:40:27
tags: 
- array
- jdk1-8
- copyof
categories: 
- java

---
## 问题详情
重装ubuntu19.10后，发现出现fctix不能正常使用，托盘图标出现，但点输入法等没反应,不能切换到中文输入法。

## 解决方法
首先删除fcitx和配置。
```bash
sudo apt remove fcitx*
rm -rf ~/.config/fcitx
```


<!--more-->


## 重新安装fcitx
```bash
sudo apt install fcitx  fcitx-rime qt4-qtconfig
```
启动qtconfig-qt4, 在现面最下面的选项，确认选择输入法能为fcitx。

命令执行：fcitx-diagnose，检查是否缺少条件。

启动fcitx，输入法可以切换，能输出中文。