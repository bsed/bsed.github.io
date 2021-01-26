---
title: "Archlinux 设置 Rime 默认英文状态"
categories: [ "Linux" ]
tags: [ "rime","archlinux" ]
draft: false
slug: "archlinux-sets-rimes-default-english-state"
date: "2019-12-14 18:06:00"
---

> Rime 的源代码托管在 Github 上，官方 Wiki 上已经讲述了 Rime 配置的种种细节。
在不同的平台上，Rime 的配置文件路径有所不同。而且，这个路径还受到输入法框架类型的影响。在 Linux 下，如果系统的输入法框架为 ibus，则这个路径是 `~/.config/ibus/rime`；如果输入法框架为 fcitx，则配置路径为 ~/.config/fcitx/rime。


<!--more-->


Rime 的配置目录文件：
![1516770799750_2.png][1]

以 yaml 结尾的，是 Rime 的数据配置文件。配置 Rime，需要修改这种类型的文件。

默认情况下，Rime 使用“朙月拼音”，对应的配置文件为 luna_pinyin.schema.yaml。如果想将它设置为默认输入英文，则将该文件中switches->ascii_mode->reset的值修改为1即可：
![1516770846870_3.png][2]

如果修改了 Rime 的默认输入法（如修改为“朙月拼音·简化字”），则配置默认输入状态时，应选择相应的配置文件进行修改。对于“朙月拼音·简化字”来说，这个配置文件是 `luna_pinyin_simp.schema.yaml`。

需要注意的是，默认输入状态的配置是针对输入法进行的，因此如果想使所有输入法都默认输入英文，则需要分别修改它们的配置文件。

最后，记得部署(deploy)一下 Rime，使配置生效。

  [1]: https://imgs.gnux.cn/usr/uploads/2019/12/1874533789.png
  [2]: https://imgs.gnux.cn/usr/uploads/2019/12/444555692.png