---
title: "在 arch linux 下 配置 fcitx-rime 输入法"
categories: [ "Linux" ]
tags: [ "rime","archlinux","archlabs" ]
draft: false
slug: "configuring-fcitxrime-input-method-under-arch-linux"
date: "2019-08-12 16:52:00"
---

## 原因

笔者之前一直用的ubuntu 19.10 系统（长达10年之久），于2019年8月份下尝试一下 archlinux 系统，于是重新在 archlabs 系统 中使用**fcitx-rime** 输入法。

## 配置

在 `~/.config/fcitx/rime/` 目录下新建 `default.custom.yaml` 文件， 将你需要更改的配置由`default.yaml` 复制到其中的 `patch:` 下。

**注意** ：rime会优先考虑patch里的设置，如：


<!--more-->


```yaml
patch:

  schema_list:
    - schema: luna_pinyin
    - schema: luna_pinyin_fluency
#    - schema: bopomofo
#    - schema: bopomofo_tw 
#    - schema: cangjie5
#    - schema: stroke
#    - schema: terra_pinyin

menu/page_size: 8
```

具体某个输入法的设置可以依葫芦画瓢，比如新建 `luna_pinyin.custom.yaml`

```yaml
patch:

  switches:
    - name: ascii_mode
      reset: 0
    states: [ 中文, 西文 ]
    - name: full_shape
      states: [ 半角, 全角 ]
    - name: simplification
      reset: 1
      states: [ 漢字, 汉字 ]
    - name: ascii_punct
      states: [ 。，, ．， ] 
```

这种做法可以让我们在轻松的保存自己的配置，以便进行同步。

### ibus-rime

ibus-rime的皮肤配置写入如下文件就好

```
~/.config/ibus/rime/default.custom.yaml
```

记录一个配置皮肤的网页工具：

```
https://rime.netlify.com/
```

## 调教主题

默认的fcitx的皮肤太丑，共有三个选项：`dark`、`default`和`classic`，太丑了，所以需要哥哥调教一下。

方式：

下载皮肤配置文件并复制到目录 `~/.config/fcitx/skin/`下就可以切换了。

推荐皮肤：

1. `fcitx-skin-material`
   archlinux下可以直接使用`pacman -S fcitx-skin-material`进行安装。
2. `mantis`
3. `fcitx-artwork`
```bash
git clone https://github.com/fcitx/fcitx-artwork
cd fcitx-artwork
cp -r skin ~/.config/fcitx/
```

## 词库添加

使用的工具 `[深蓝词库转换](https://github.com/studyzy/imewlconverter)`：

笔者在windows下运行，当然你可以在 arch 下下载 mono 程序运行深蓝词库转换。

然后下载你所需要的[搜狗细胞词库](https://pinyin.sogou.com/dict/cate/index/)：


导入到 `深蓝词库转换`，然后转换为 `Rime输入法` 格式。
将转换后的内容写在以 `***.dict.yaml` 命名的文件中，如：

```yaml
# luna_pinyin
# Rime dictionary 计算机
# encoding: utf-8

---
name: luna_pinyin.computer
version: "2019.08.12"
sort: by_weight
use_preset_vocabulary: true
import_tables:
  - luna_pinyin
  - luna_pinyin.computer
...

阿姆达尔定律    a mu da er ding lv    1
阿帕网    a pa wang    1
埃尔布朗基    ai er bu lang ji    1
埃尔米特函数    ai er mi te han shu    1
埃克特    ai ke te    1
```

其中以下部分需要自己添加，此格式格式所需不可缺少。

```
---
name: luna_pinyin.computer
version: "2019.08.12"
sort: by_weight
use_preset_vocabulary: true
import_tables:
  - luna_pinyin
  - luna_pinyin.computer
...
```

然后将该文件复制到 `~/.config/fcitx/rime/` 下，在 `luna_pinyin.custom.yaml` 中添加以下内容即可

```yaml
#添加计算机词库，来自搜狗细胞词库转换而来
translator:
  dictionary: luna_pinyin.computer
```

