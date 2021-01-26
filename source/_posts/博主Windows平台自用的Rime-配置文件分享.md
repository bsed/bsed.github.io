---
title: "博主Windows平台自用的Rime 配置文件分享"
categories: [ "日常" ]
tags: [ "rime","小河双拼" ]
draft: false
slug: "rime-profile-sharing-for-bloggers"
date: "2020-04-05 18:57:00"
---

  ![gn_windows_rime_config2020.png][1]

看起来还不错.

需要将下载下来的zip 放到 `C:\Users\arjvik\AppData\Roaming\Rime` 文件夹下;

笔者配置的快捷方式是 `ctrl+shilf+f12`
<!--more-->
见代码(default.custom.yaml):
```
patch:
  menu:
    page_size: 8
  schema_list:
  - { schema: double_pinyin_flypy }
  - schema: luna_pinyin_simp      # 朙月拼音 简化字
  "switcher/hotkeys":
  - "Control+Shift+F12"
  ascii_composer/good_old_caps_lock: true
  ascii_composer/switch_key:
    Caps_Lock: commit_code
    Control_L: noop
    Control_R: noop
    # 按下左 shift 英文字符直接上屏，不需要再次回车，输入法保持英文状态
    Shift_L: commit_code
    Shift_R: noop
```

输入表情符合（`/bq`）:

![gn_windows_rime_config2020_bq.png][2]
 


其他特色功能可以自己发现....

仓库: [https://github.com/bsed/rime-config-win](https://github.com/bsed/rime-config-win)


  [1]: https://imgs.gnux.cn/usr/uploads/2020/04/1263193966.png
  [2]: https://imgs.gnux.cn/usr/uploads/2020/04/4284620074.png