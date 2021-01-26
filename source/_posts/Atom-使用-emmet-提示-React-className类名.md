---
title: "Atom 使用 emmet 提示 React className类名"
categories: [ "Css" ]
tags: [ "atom","emmet","react" ]
draft: false
slug: "atom-emmet-react-classname"
date: "2016-12-25 16:28:00"
---

> 这个小技巧节省了我写的`React / JSX`的时间。没有Atom键盘映射代码，你需要`ctrl + e`每次你想展开一个标签。这很快就变成了一种痛苦。使用下面的步骤简化它。

```html
div.container (hit tab)
>>    <div className="container"></div>
```


<!--more-->


## 安装react 插件
```bash
apm install react
```
## 安装Emmet 插件
```bash
Atom > Preferences > Install  (搜索 Emmet & 并 install)
```
打开编辑下面的键盘映射`keymap.cson`

在` keymap.cson` 添加如下 `key bindings`
```
'atom-text-editor[data-grammar="source js jsx"]:not([mini])':
  'tab': 'emmet:expand-abbreviation-with-tab'
```

假如更改没有效果的话，退出 atom 重新启动就好了。


  [1]: https://imgs.gnux.cn/usr/uploads/2016/12/4019735758.png