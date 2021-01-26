---
title: "Linux 上 Rime 输入法配置指南"
categories: [ "Linux" ]
tags: [ "rime" ]
draft: false
slug: "guide-for-configuration-of-rime-input-method-on-linux"
date: "2018-09-02 03:29:00"
---

![25704-x2mr5wnxwyb.png](https://imgs.gnux.cn/usr/uploads/2018/09/2415429899.png)
Linux 上不支持小狼毫的配色，可惜了一阵，不过其配置依然很强大。

*注意*： 需要个人修改配置的请联系我: <a href=mailto:me@gnux.cn>电邮</a>
。
## 簡介
Rime 的源代码托管在 Github 上, 官方配置见 [Rime定制指南](https://github.com/rime/home/wiki/CustomizationGuide)

在不同的平台上，Rime 的配置文件路径有所不同。而且，这个路径还受到输入法框架类型的影响。在 Linux 下，如果系统的输入法框架为 ibus，则这个路径是 `~/.config/ibus/rime`；如果输入法框架为 fcitx，则配置路径为 `~/.config/fcitx/rime`。

Rime 的配置目录如下
![79237-tlg68aaxs6c.png](https://imgs.gnux.cn/usr/uploads/2018/09/3713598901.png)


<!--more-->


以 `yaml` 结尾的，是 Rime 的数据配置文件。配置 Rime，需要修改这种类型的文件。

默认情况下，Rime 使用“朙月拼音”，对应的配置文件为 `luna_pinyin.schema.yaml`。如果想将它设置为默认输入英文，则将该文件中`switches->ascii_mode->reset`的值修改为1即可：
![72284-2jastcuqth1.png](https://imgs.gnux.cn/usr/uploads/2018/09/726502416.png)

如果修改了 Rime 的默认输入法（如修改为“朙月拼音·简化字”），则配置默认输入状态时，应选择相应的配置文件进行修改。对于“朙月拼音·简化字”来说，这个配置文件是 `luna_pinyin_simp.schema.yaml`。

需要注意的是，默认输入状态的配置是针对输入法进行的，因此如果想使所有输入法都默认输入英文，则需要分别修改它们的配置文件。

最后，记得**部署(deploy)**一下 Rime，使配置生效。
如图:

![2018_09_02_13_01_16_screenshot_.jpg][1]
## `Schema.yaml` 文件介绍

### 描述档〔schema:〕

- `schema_id:` 方案内部名，在代码中引用此方案时以此名为正，通常由英文、数字、下划线组成
- `name:` 方案的显示名称〔即方案选单中以示人的，通常为中文〕
- `version:` 版本号，在发布新版前请确保已升版本号
- `author:` 发明人、撰写者。如果您对方案做出了修改，请保留原作者名，并将自己的名字加在后面
- `description:` 请简要描述方案历史、码表来源、该方案规则等
- `dependencies:` 如果本方案依赖于其它方案〔通常来说会依赖其它方案做为反查，抑或是两种或多种方案混用时〕


<!--more-->


### 开关〔switches:〕

- `ascii_mode` 是中英文转换开关。预设`0`为英文，`1`为中文
- `full_shape` 是全角符号/半角符号开关。注意，开启全角时英文字母亦为全角。`0`为半角，`1`为全角
- `extended_charset` 是字符集开关。`0`为CJK基本字符集，`1`为CJK全字符集
  - 仅`table_translator`可用
- `simplification` 是转化繁简开关。一般情况下与上同，`0`为不开启转化，`1`为转化。
- `ascii_punct` 是中西文标点转换开关，`0`为中文句读，`1`为西文标点。

## 重新部署

更改的 `default.yaml` 只会在重新部署后生效。部署时间会受所用字典大小的影响，部署期间无法输出中文。部署完毕后，看能否通过 Ctrl+` 唤出方案选单，若输入方案无法正常使用，则设置错误，未部署成功。以下分别是 Ibus 框架和 Fcitx 框架的部署方法。

1.IBus：右击输入法图标后，点击 Deploy，或者

```
rm ~/.config/ibus/rime/default.yaml; ibus-daemon -drx
```

2.Fcitx：右击输入法图标后，点击 Deploy

## 定制

主要修改用户资料夹 `~/.config/ibus/rime/`（IBus） 或 `~/.config/fcitx/rime/`（Fcitx） 中的 `.yaml` 文档。以 Fcitx 为例，下面是我的详细定制过程：

1.添加小鶴雙拼、五笔86

```
# 添加小鹤双拼、86 版五笔
cp /usr/share/rime-data/double_pinyin_flypy.schema.yaml /usr/share/rime-data/wubi86.schema.yaml ~/.config/fcitx/rime/
```

若习惯自然码双拼，则：

```
# 添加自然码双拼
cp /usr/share/rime-data/double_pinyin.schema.yaml ~/.config/fcitx/rime/
```

2.定制方案选单，仅保留朙月拼音、小鶴雙拼、五笔86

```
# default.yaml
# ...

schema_list:
  - schema: luna_pinyin
  - schema: double_pinyin_flypy
  - schema: wubi86
```

重新部署即可生效，以下部署过程略过。

3.定制唤出方案选单的快捷键

唤出方案选单，默认的快捷键为 Ctrl+` 或 F4 或 Ctrl+Shift+`。但是在我的电脑上 Ctrl+Shift+` 与 VSCode 的快捷键冲突，F4 有的游戏里面会用到，所以保留一个 Ctrl+` 即可。

```
# default.yaml
# ...

switcher:
  caption: 〔方案選單〕
  hotkeys:
    - Control+grave
```

4.每页候选数改为9

默认每页至多显示 5 个候选项，而允许的范围是 1〜9。

```
# default.yaml
menu:
  page_size: 9
```

5.分别定制【朙月拼音】、【小鶴雙拼】、【五笔86】的标点符号

个人习惯以 `\` 键直接输入标点「、」。

```
# luna_pinyin.schema.yaml
# double_pinyin_flypy.schema.yaml
# wubi86.schema.yaml
# ...

punctuator:
  import_preset: default
  half_shape:
    '\' : '、'
    '#' : '#'
    '/' : '/'
    '`' : '`'

```

解释：

`punctuator` 是 Rime 中负责转换标点符号的组件。该组件会从设定中读取符号映射表，而知道该做哪些转换。

`import_preset` 是说，本方案要继承一组预设的符号映射表、要从另一个设定档 `default.yaml` 导入。

查看 `default.yaml` ，确有如下符号表：

```
# default.yaml
# ...

punctuator:
full_shape:
  '/' : [ ／, ÷ ]
  '\' : [ 、, ＼ ]
half_shape:
  '/' : [ 、, '/', ／, ÷ ]
  '\' : [ 、, '\', ＼ ]
```

这里的含义是在由 `default` 导入的符号表之上，覆写对按键 `/` 的定义。

当然，也可以导入其他的符号映射表，同样覆写设定，见下面的定制。

6.分别定制【朙月拼音】、【小鶴雙拼】的简化字输出

日常使用时以简化字为主，每每需要敲 Ctrl+` 组合键、从〔方案选单〕中选择「漢字→汉字」或 「朙月拼音・简化字]，切换十分不便，故设定默认输出简化字。

将：

```
# luna_pinyin.schema.yaml
# luna_pinyin_fluency.schema.yaml
# ...

switches:
  - name: simplification
    states: [ 漢字, 汉字 ]
```

改为：

```
# luna_pinyin.schema.yaml
# luna_pinyin_fluency.schema.yaml
# ...

switches:
  - name: zh_simp
    states: [ 汉字, 汉字 ]
```

7.分别定制朙月拼音、小鶴雙拼、五笔86的特殊符号输入

导入 symbol.yaml，以使用 `/` 开头打印特殊字符，虽然 fcitx 有自带的软键盘，但并不全：

```
# luna_pinyin.schema.yaml
# luna_pinyin_fluency.schema.yaml
# wubi86.schema.yaml
# ...

punctuator:
  import_preset: symbols

recognizer:
  patterns:
    punct: "^/([a-z]+|[0-9])$"
```

使用方法：打开 symbol.yaml 看到的

```
# ...
'/fh': [ ©, ®, ℗, ℠, …… ]
# ...
```

等等，表示输入 `/fh` 后，就会出现之后的字符。

8.定制 fcitx 皮肤

```
# 推荐两款皮肤
# Material
cd tmp
git clone https://github.com/ootaharuki99/fcitx-skin-material
cd fcitx-skin-material
cp -r material ~/.config/fcitx/skin/

# 使用 Mantis
cd tmp
git clone https://github.com/fcitx/fcitx-artwork
cd fcitx-artwork
cp -r skin ~/.config/fcitx/
```

9.词库定制：分别给【朙月拼音】、【小鶴雙拼】添加一个常用词库

词库包括：

- 由系统文本词库（xxx.dict.yaml）通过「重新部署/deploy」生成的固态词典（xxx.table.bin）
- 记录用户输入习惯的用户词典（xxx.userdb.kct），允许用户自行删除（shift+delete）

如从从[词库_法律词库_搜狗输入法](https://pinyin.sogou.com/dict/cate/index/85?rf=dictindex)下载官方推荐的法律词库，用 wine 运行 [深蓝词库转换](https://github.com/studyzy/imewlconverter)，将搜狗 scel 格式的细胞词库 转为 Rime 中州韵词库格式的文本，保存为 luna_pinyin.law.dict.yaml 文件：

```
# Rime dictionary
# encoding: utf-8

---
name: luna_pinyin.law
version: "2018.01.02"
sort: by_weight
use_preset_vocabulary: true
import_tables:
  - luna_pinyin
...

阿奎那 a kui na  1
阿姆斯里克 a mu si li ke 1
暗娼  an chang 1
按份共有 an fen gong you 1
按份赔偿责任 an fen pei chang ze ren  1
# ...
```

新建 luna_pinyin.extended.dict.yaml，可用 cat 命令直接生成：

```
cat << EOF > ~/.config/fcitx/rime/luna_pinyin.extended.dict.yaml
# Rime dictionary
# encoding: utf-8

---
name: luna_pinyin.extended
version: "2018.01.02"
sort: by_weight
use_preset_vocabulary: true
import_tables:
  - luna_pinyin
  - luna_pinyin.law
EOF
```

修改朙月拼音、小鶴雙拼的配置文件：

```
# luna_pinyin.schema.yaml
# luna_pinyin_fluency.schema.yaml
# ...

translator:
  dictionary: luna_pinyin.extended
```

也可从贴吧下载别人已经制作好的词库。

9.设置五笔 86 无重码自动上屏

```
# 添加以下
# wubi86.schema.yaml
# ...
speller:
  max_code_length: 4
  auto_select: true
  auto_select_unique_candidate: true
```

其中：

```
max_code_length: 4                      # 最长 4 码
auto_select: true                       # 顶字上屏
auto_select_unique_candidate: true      # 无重码自动上屏
```

10.默认英文输出

有些用户习惯默认英文输出，在需要用中文时再做切换，只需在方案中重设状态开关初始值即可：

以【朙月拼音】为例：

```
# luna_pinyin.schema.yaml
# ...

switches:
  - name: ascii_mode
    reset: 1
    states: [ 西文, 中文 ]
```

11.中西文切换快捷键设置

略过。

先解释一下默认的按键配置：

```
# default.yaml
# ...

ascii_composer:
  good_old_caps_lock: true
  switch_key:
    Shift_L: inline_ascii
    Shift_R: commit_text
    Control_L: noop
    Control_R: noop
    Caps_Lock: clear
    Eisu_toggle: clear
```

可用的按键有 Caps_Lock, Shift_L, Shift_R, Control_L, control_R，分别是大写键、左 Shift 键、右 Shift 键、左 Ctrl 键、右 Ctrl 键。

按切换键，设定输入法中西文切换的形式可选的临时切换策略有三：

- inline_ascii 在输入法的临时西文编辑区内输入字母、数字、符号、空格等，回车上屏后自动复位到中文
- commit_text 已输入的候选文字上屏并切换至西文输入模式
- commit_code 已输入的编码字符上屏并切换至西文输入模式
- 设为 noop，屏蔽该切换键

可设定的策略：

- 如果要把 Caps Lock 设为只改变字母的大小写而不做中西文切换，可将 Caps_Lock 对应的切换方式设为 noop
- 如果要以 Caps Lock 切换到西文模式，默认输出小写字母，请置 ascii_composer/good_old_caps_lock: false
- 如果要以 Caps Lock 切换到西文模式，默认输出大写字母，请使用以下设置：

```
# default.yaml
# ...

patch:
  ascii_composer/good_old_caps_lock: true
  ascii_composer/switch_key:
    Caps_Lock: commit_code
    Shift_L: noop
    Shift_R: noop
    Control_L: commit_code
    Control_R: commit_code
```

12.方便地输入含数字的西文用户名

通常，输入以小写拉丁字母组成的编码后，数字键的作用是选择相应序号的候选字。假设我的用户名是 zhongguo1949，则需要在输入 zhongguo 之后上屏或做临时中西文切换，方可输入数字部分。为了更方便输入我的用户名 `zhongguo1949`，设置一组特例，将 `zhongguo1949` 与其后的数字优先识别西文：

以【朙月拼音】为例：

```
# luna_pinyin.schema.yaml
# ...

recognizer:
  patterns:
    zhongguo1949: "^zhongguo[0-9]+$"
```

13.以回车键清除编码兼以分号、单引号选字

适合五笔、郑码等形码输入法：

以【五笔86】为例：

```
# wubi86.schema.yaml
# ...

key_binder:
  import_preset: default
  bindings:
    - { when: composing, accept: Return, send: Escape }
    - { when: has_menu, accept: semicolon, send: 2 }
    - { when: has_menu, accept: apostrophe, send: 3 }
```

14.关闭逐键提示

`table_translator` 默认开启逐键提示。若要只出精确匹配输入码的候选字，可关闭这一选项。

以【五笔86】为例：

```
# wubi86.schema.yaml
# ...

translator:
  enable_completion: false
```

15.关闭码表输入法连打

注：这个选项仅针对 `table_translator`，用于屏蔽仓颉、五笔中带有太极图章「☯」的连打词句选项，不可作用于拼音、注音、速成等输入方案。

以【五笔86】为例：

```
# wubi86.schema.yaml
# ...

translator:
  enable_sentence: false
```

16.空码时按空格键清空输入码

首先需要关闭码表输入法连打（参见上文），这样才可以在打空时不出候选词。

以【五笔86】为例：

```
# wubi86.schema.yaml
# ...

translator:
  enable_sentence: false

key_binder:
  bindings:
    - {when: has_menu, accept: space, send: space}
    - {when: composing, accept: space, send: Escape}
```

17.模糊音

以【朙月拼音】为例：

```
# luna_pinyin.schema.yaml
# ...

speller:
  algebra:
    - erase/^xx$/                      # 第一行保留
    - derive/^([zcs])h/$1/             # zh, ch, sh => z, c, s
    - derive/^([zcs])([^h])/$1h$2/     # z, c, s => zh, ch, sh
    - derive/^n/l/                     # n => l
    - derive/^l/n/                     # l => n

    # 這兩組一般是單向的
    #- derive/^r/l/                     # r => l
    #- derive/^ren/yin/                 # ren => yin, reng => ying
    #- derive/^r/y/                     # r => y

    # 下面 hu <=> f 這組寫法複雜一些，分情況討論
    #- derive/^hu$/fu/                  # hu => fu
    #- derive/^hong$/feng/              # hong => feng
    #- derive/^hu([in])$/fe$1/          # hui => fei, hun => fen
    #- derive/^hu([ao])/f$1/            # hua => fa, ...

    #- derive/^fu$/hu/                  # fu => hu
    #- derive/^feng$/hong/              # feng => hong
    #- derive/^fe([in])$/hu$1/          # fei => hui, fen => hun
    #- derive/^f([ao])/hu$1/            # fa => hua, ...

    # 韻母部份
    #- derive/^([bpmf])eng$/$1ong/      # meng = mong, ...
    #- derive/([ei])n$/$1ng/            # en => eng, in => ing
    #- derive/([ei])ng$/$1n/            # eng => en, ing => in
```

【明月拼音·简化字／台湾正体／语句流】也适用，只须将模板保存到 `luna_pinyin_simp.custom.yaml` 、 `luna_pinyin_tw.custom.yaml` 或 `luna_pinyin_fluency.custom.yaml` 。

18.设置备份文件夹路径

修改 installation.yaml 即可。

## 自定义 Fcitx 皮肤

以下其坑了。

1.皮肤文件复制到 ~/.config/fcitx/skin 或 /usr/share/fcitx/skin

2.推荐：

- [fcitx-artwork](https://github.com/fcitx/fcitx-artwork) 的 Mantis 和 Darkmouth
- [fcitx-skin-material](https://github.com/ootaharuki99/fcitx-skin-material)
- [fcitx-mint-skin](https://github.com/fu-sen/fcitx-mint-skin)
- [ubuntu forum](http://forum.ubuntu.org.cn/viewforum.php?f=8)

更多皮肤：

- [FCITX皮肤合集](https://www.deepin.org/2011/12/17/fcitx-skins/)

- [FCITX 皮肤10款](https://www.deepin.org/2012/01/04/10-fcitx-skins/)

- [基于sogou拼音皮肤修改的fcitx皮肤](https://github.com/hosxy/fcitx-skin)

  ```
  mkdir -p ~/.config/fcitx/skin \
  && cd ~/.config/fcitx/skin \
  && git clone https://github.com/whonchen/fcitx-skin.git
  ```

- [fcitx-artwork](https://github.com/fcitx/fcitx-artwork)

```
# Mantis/Darkmouth
cd tmp
git clone https://github.com/fcitx/fcitx-artwork
cd fcitx-artwork
cp -r skin ~/.config/fcitx/

# material
git clone https://github.com/ootaharuki99/fcitx-skin-material

# mint-dark
git clone https://github.com/fu-sen/fcitx-mint-skin
```

3.以 material 为例，对皮肤的设定文件 fcitx_skin.conf 进行详解：

```
[SkinInfo]
# 皮肤名称
Name=Material
# 皮肤版本
Version=0.3
# 皮肤作者
Author=HRKo
# 描述
Desc=Material

[SkinFont]
# 输入字体大小
FontSize=12
# 菜单字体大小
MenuFontSize=10
# 字体大小遵守dpi设置
# Available Value:
# True False
RespectDPI=True
# 提示信息颜色
TipColor=158 158 158
# 输入信息颜色
InputColor=220 220 220
# 候选词索引颜色
IndexColor=158 158 158
# 第一候选词颜色
FirstCandColor=33 33 33
# 用户词组颜色
UserPhraseColor=33 33 33
# 码表提示颜色
CodeColor=158 158 158
# 其他颜色
OtherColor=117 117 117
# 活动菜单项颜色
ActiveMenuColor=0 0 0
# 非活动菜单项颜色
InactiveMenuColor=33 33 33

[SkinMainBar]
# 背景图片
BackImg=bar.png
# Logo图标
Logo=logo.png
# 英文模式图标
Eng=null
# 激活状态输入法图标
Active=null
# 左边距
MarginLeft=8
# 右边距
MarginRight=8
# 上边距
MarginTop=4
# 下边距
MarginBottom=8
# 纵向填充规则
# Available Value:
# Copy Resize
#FillVertical=Resize
# 横向填充规则
# Available Value:
# Copy Resize
#FillHorizontal=Resize
# 使用自定的文本图标颜色
# Available Value:
# True False
#UseCustomTextIconColor=False
# 活动的文本图标颜色
#ActiveTextIconColor=0 0 0
# 非活动的文本图标颜色
#InactiveTextIconColor=0 0 0
# 特殊图标位置
#Placement=

[SkinInputBar]
# 背景图片
BackImg=input.png
# 左边距
MarginLeft=14
# 右边距
MarginRight=14
# 上边距
MarginTop=3
# 下边距
MarginBottom=7
# 光标颜色
CursorColor=200 200 200
# 预编辑文本的位置或偏移
InputPos=0
# 候选词表的位置或偏移
OutputPos=0
# 上一页图标
BackArrow=prev.png
# 下一页图标
ForwardArrow=next.png
# 上一页图标
BackArrowX=33
# 下一页图标
BackArrowY=5
# 上一页图标
ForwardArrowX=15
# 下一页图标
ForwardArrowY=5
# 纵向填充规则
# Available Value:
# Copy Resize
#FillVertical=Resize
# 横向填充规则
# Available Value:
# Copy Resize
# FillHorizontal=Copy

[SkinTrayIcon]
# 活动输入法图标
Active=active.png
# 非活动输入法图标
Inactive=inactive.png

[SkinMenu]
# 背景图片
BackImg=menu.png
# 上边距
MarginTop=4
# 下边距
MarginBottom=7
# 左边距
MarginLeft=5
# 右边距
MarginRight=5
# 活动菜单颜色
ActiveColor=238 238 238
# 分隔线颜色
LineColor=255 255 255
# 纵向填充规则
# Available Value:
# Copy Resize
# FillVertical=Copy
# 横向填充规则
# Available Value:
# Copy Resize
# FillHorizontal=Copy

[SkinKeyboard]
# 虚拟键盘图片
BackImg=keyboard.png
# 软键盘按键文字颜色
KeyColor=55 71 79
```

自定义 material 的颜色内容，感觉太淡了：

```
# 原方案
TipColor=158 158 158
InputColor=220 220 220
IndexColor=158 158 158
FirstCandColor=33 33 33
UserPhraseColor=33 33 33
CodeColor=158 158 158
OtherColor=117 117 117
ActiveMenuColor=0 0 0
InactiveMenuColor=33 33 33

# 方案一
TipColor=239 41 41
InputColor=61 173 229
IndexColor=119 119 119
FirstCandColor=52 101 164
UserPhraseColor=245 121 0
CodeColor=8 157 36
OtherColor=68 68 68
ActiveMenuColor=55 55 55
InactiveMenuColor=70 76 78
```

4.Fcitx 输入法皮肤制作

放弃放弃放弃！等待下次的心血来潮补完。

一直对 win 上小狼毫基于配色的皮肤默默不忘，这种皮肤我只看得小狼毫这一种，曾经热衷于手心输入法的时候，用九宫格皮肤的形式勉强实现了一下，但因为软件皮肤本身的问题，无法完美。这里也是用九宫格的方式复制小狼毫的皮肤，当然无法完美。以下是任务列表：

- 输入面板
  - 背景图片，即你打字时候的面板：input.png
  - 输入面板上的前后箭头：prev/next.png
- 系统托盘的右键菜单：menu.png
- 功能面板
  - 背景图片，即你调出输入法现在在屏幕上的那个东西：bar.png
  - 功能面板上的 logo 及以下：logo.png
  - 键盘布局：en/cn.png
  - 通用的开关：active/inavtive.png
  - 繁简切换开关两个，繁体/简体：`chttrans_active.png/chttrans_inactive.png`
  - 虚拟键盘开关两个：`vk_active.png/vk_inactive.png`
  - 全角半角开关两个，全角/半角：`fullwidth_active.png/fullwidth_inactive.png`
  - 中英标点两个，中/英：`punc_active.png/punc_inactive.png`
  - 双拼联想开关两个：`remind_active.png/remind_inactive.png`
  - 输入法图标：pinyin.png/wubi.png
- 虚拟键盘：keyboard.png
- 定位配置：fcitx_skin.conf

JUST DO IT! 以实现 weasel 的"現代戰爭／Modern Warfare" 为例：

[![Modern Warfare](https://i.zongwei.me/images/2018/06/05/2ab3fdbcb009477f3a07591132f4475f.jpg)](https://i.zongwei.me/images/2018/06/05/2ab3fdbcb009477f3a07591132f4475f.jpg)

```
# Weasel settings
# encoding: utf-8

style:                       # 预置界面样式，如字体、字号、横排/直排等
  color_scheme: aqua         # 这项用于选中下面定义的aqua方案
  font_face: Microsoft YaHei # 字体名称：微软雅黑
  font_point: 14             # 字号
  horizontal: false          # 定制小狼毫外观设定---候选横排
  fullscreen: false          # 演示模式（全屏的输入窗口）
  inline_preedit: false      # 内嵌编码（仅支持TSF）
  display_tray_icon: false   # 显示托盘图标
  layout:                    # 窗口布局
    min_width: 160           # 宽度
    min_height: 0            # 高度
    border_width: 3          # 窗口边界宽度，大於圆角半径才有效果
    margin_x: 12             # 边缘x距
    margin_y: 12             # 边缘y距
    spacing: 10              # 间距
    candidate_spacing: 5     # 候选间距
    hilite_spacing: 4        # 第一候选项，间隔
    hilite_padding: 2        # 第一候选项，填充
    round_corner: 4          # 圆的拐角

preset_color_schemes:
  modern_warfare:                          # 在配色方案列表里加入标识为 starcraft 的新方案
    name: "現代戰爭／Modern Warfare"
    author: P1461
    text_color: 0x14bc70                   # 编码行已上屏的文字颜色，24位色值，用十六进制书写方便些，顺序是蓝绿红0xBBGGRR
    back_color: 0x0a1b0d                   # 背景底色
    border_color: 0x4bad83                 # 背景边框颜色，与底色相同则为无边框的效果
    hilited_text_color: 0xfbfdfc           # 编码行未上屏的拼音颜色，即与当前高亮候选对应的那部份输入码
    hilited_back_color: 0x030e06           # 编码行未上屏的拼音底色，可起到高亮效果
    candidate_text_color: 0xabfedc         # 未选中的候选项文字颜色
    comment_text_color: 0xfcfdfb           # 未选中的候选项提示拼音颜色
    hilited_candidate_text_color: 0xabfedc # 选中的候选项文字颜色
    hilited_candidate_back_color: 0x676f63 # 选中的候选项背景颜色
```

首先两者的颜色关系：

```
# 提示信息颜色=未选中的候选项提示拼音颜色，comment_text_color
TipColor=252 252 252
# 输入信息颜色
InputColor=0 0 0
# 候选词索引颜色
IndexColor=158 158 158
# 第一候选词颜色，hilited_candidate_text_colol
FirstCandColor=
# 用户词组颜色
UserPhraseColor=33 33 33
# 码表提示颜色
CodeColor=158 158 158
# 其他颜色
OtherColor=117 117 117
# 活动菜单项颜色
ActiveMenuColor=0 0 0
# 非活动菜单项颜色
InactiveMenuColor=33 33 33
```

使用 Inkscape 设计图片，首先设计 input.png，智障步骤：

1.打开 inkscape，选左边的创建矩形或正方形，画一个方形，在上面的工具配置栏设置宽高各 40px。

2.鼠标点住右上角的点，往下拖可定制圆角，在工具配置栏设置 Ry 圆角为 2px。

3.右击图像，选择填充和笔刷，点击填充单色，设置 RGBA 为 0a1b0dff。不透明度 98%。

4.依次点击文件 -> 文档属性 -> 自定义尺寸中的缩放页面到内容 -> 上下左右各 5px -> 缩放内容到绘图或选区

5.右击图像，点击再制，拖到下面备用

6.设置原图宽高各 42px，右击复制再粘贴生成新图像，图像移到左边备用

7.把原图填充单色，RGBA: 4bad83ff，Shift 选中此图与之前拖到下面备用的图片，按 Ctrl + Shift + A 唤出对齐和分散窗格，选择相对于最大对象，然后点击垂直居中、水平居中，在然后选择路径 -> 差集，边框制成，并设置边框 RGBA: 4bad83ff，对象 -> 置于顶层

8.Shift 选中边框和拖到左边备用的图片，垂直居中、水平居中

9.导出 png

```
# 亦可用 ImageMagick 的 convert 命令生成，但是非常地不好看
# 输入面板背景图片
# 使用 -border 3 -bordercolor "#4bad83" 无效
# xc:'#4bad83' = -background '#4bad83' ...
# -stroke red -strokewidth 2
convert -size 152x22 xc:'#4bad83' -fill '#0a1b0d' -draw "roundRectangle 1,1 149,19 2,2" rect-2.png
convert -size 400x400 xc:'#4bad83' -fill '#0a1b0d' -draw "roundRectangle 3,3 396,396 2,2" input.png
convert -size 40x40 xc:'#4bad83' -fill '#0a1b0d' -draw "roundRectangle 3,3 36,36 2,2" input.png

# 输入面板上的前后箭头：prev/next.png
# Font Awesome 5
# chevron-left/chevron-left
# caret-left/caret-left
# angle-left/angle-left
# 右击图片 -> 填充和笔刷 -> 填充 -> 单色 -> RGBA: 14bc70ff
# 4x8 18x18
# 使用 Ikscape 将 svg 导出为 png
# 12x20

# 系统托盘的右键菜单
# menu.png
# 同 input.png

# 功能面板背景图片
# bar.png
# 同 input.png
ln -sf input.png menu.png
ln -sf input.png bar.png
ln -sf input.png menu.png

# 功能面板上的 logo 及以下：logo.png

# 键盘布局：en/cn.png

# 通用的开关：active/inavtive.png

# 繁简切换开关两个，繁体/简体：`chttrans_active.png/chttrans_inactive.png`

# 虚拟键盘开关两个：`vk_active.png/vk_inactive.png`

# 全角半角开关两个，全角/半角：`fullwidth_active.png/fullwidth_inactive.png`

# 中英标点两个，中/英：`punc_active.png/punc_inactive.png`

# 双拼联想开关两个：`remind_active.png/remind_inactive.png`

# 输入法图标：pinyin.png/wubi.png

# 虚拟键盘：keyboard.png

# 定位配置：fcitx_skin.conf

```

## References

- [Rime_collections](https://github.com/LEOYoon-Tsaw/Rime_collections)
- [致第一次安装 RIME 的你之修订版](http://tieba.baidu.com/p/3288634121)
- [[ 史前大坑 ]Fcitx 官方 Artwork 团队出品：Fcitx 输入法皮肤制作全教程](<https://forum.suse.org.cn/t/fcitx-artwork-fcitx/731/10)>
- [致第一次安装RIME的你](https://www.zybuluo.com/eternity/note/81763)
- [小狼毫输入法(weasel)的使用经验分享](http://tieba.baidu.com/p/1966457374)
- [「Rime词库扩展—为小狼毫打造一个强大好用的词库OpenCC重](http://tieba.baidu.com/p/3974457224)
- [「Rime增强计划」——让小狼毫更好用のBetterRime增强包で](http://tieba.baidu.com/p/4125987751)
- [自己写的一个小狼毫配置工具](http://tieba.baidu.com/p/4436681489)
- [〔新手推荐敎程〕关于导入词库及「深蓝词库转换」的正确操](http://tieba.baidu.com/p/2757690418?see_lz=1)
- [RIme自己定制过程【献给仍旧不太熟练的使用者】](http://tieba.baidu.com/p/3773090625?see_lz=1)


  [1]: https://imgs.gnux.cn/usr/uploads/2018/09/2764682271.jpg