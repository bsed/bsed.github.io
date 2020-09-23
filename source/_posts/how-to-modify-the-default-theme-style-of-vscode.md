---
title: 如何修改vscode默认主题样式
date: 2016-10-29 09:38:00
updated: 2016-10-29 10:08:02
tags: 
- git
categories: 
- linux

---
> 修改 vscode 的默认`dark theme` 的默认样式，并给状态条添加透明度。

## 方法一

```bash
echo ".monaco-workbench>.part.statusbar{background-color:#000;}" | sudo tee -a /usr/share/code-insiders/resources/app/out/vs/workbench/workbench.main.css
```
**注意**： `code-insiders` 改成自己的vscode 安装路径名字`code`，但是当更新 vscode 时候需要重新更新这条命令。
 
## 方法二

使用自定义css插件(Custom CSS)修改  

github: [https://github.com/be5invis/vscode-custom-css](https://github.com/be5invis/vscode-custom-css)

### 安装

```
ext install vscode-custom-css

```
或者 vscode 插件目录下搜索 `vscode-custom-css` `安装

### 配置

 - 新建默认样式目录文件夹 `/home/kelvin/tools/css`
 - 在里面建立css 样式文件 `style.css`
 - 全局启用插件，步骤*见下面1*
 - 添加样式内容, 步骤*见下面2*

**下面1**:

打开文件 `File->Preferences->User Settings`  添加启用配置：
```json
//custom css
  "vscode_custom_css.imports": [
    "file:///home/kelvin/tools/css/style.css"
  ],
```

**下面2**:

打开style.css 文件，添加修改css内容
```css
.monaco-workbench>.part.statusbar{background-color:#000;}
```

**注意**：此插件可以和 [vscode-icons](https://github.com/robertohuertasm/vscode-icons) 插件共存

下面提供[be5invis](https://github.com/be5invis)的修改样式.

```css
/***** PATCHES *****/
.monaco-workbench.hc-black { color: #FFF; background-color: #000; }
.monaco-workbench.hc-black .sidebar .separator  { background-color: #000; border: 1px solid #6FC3DF; }
.monaco-workbench.hc-black input,
.monaco-workbench.hc-black textarea {
    background-color: #000;
}
.monaco-workbench.hc-black .monaco-action-bar .action-item.disabled .action-label.disabled,
.monaco-workbench.hc-black .monaco-action-bar .action-item.disabled .action-label.disabled:hover {
    opacity: 1;
}
.monaco-workbench.hc-black .monaco-action-bar .action-item.disabled .action-label.disabled:before {
    opacity: .4;
}

.monaco-shell { font-family: "Iosevka", "Helvecita", "Inziu Iosevka SC", sans-serif}
.monaco-shell:lang(zh-Hans) { font-family: "Iosevka", "Helvecita", "Inziu Iosevka SC", sans-serif}
.monaco-shell:lang(zh-Hant) { font-family: "Iosevka", "Helvecita", "Inziu Iosevka CL", sans-serif}
.monaco-shell:lang(ja) { font-family: "Iosevka", "Helvecita", "Inziu Iosevka J", sans-serif}
.monaco-workbench > .part > .content {font-size:11px}
.decorationsOverviewRuler{
    clip:rect(2px,13px,1000px,2px);
    margin-top: -2px;
    height:calc(100% + 4px);
}
.monaco-workbench.vs-dark .editor > .content > .editor-center,.monaco-workbench.vs-dark .editor > .content > .editor-right { border-left-color:#0a0a0a}
.monaco-workbench.vs-dark { background: #252525 }
.monaco-editor.vs-dark .git-dirty-modified-diff-glyph { background-color: rgba(250,190,28,0.6) }
.explorer-viewlet .explorer-item-label, .explorer-viewlet .working-files-item-label { color: rgb(190, 190, 190) }
.vs-dark .monaco-tree .monaco-tree-row.selected { color: rgb(210, 210, 210) }
.vs-dark .monaco-tree .monaco-tree-row.focused, .vs-dark .monaco-tree .monaco-tree-row.selected { background: rgb(57,57,56)!important }
.vs-dark .monaco-tree .primary-action-bar,.vs-dark .monaco-tree.focused .monaco-tree-row.focused:not(.highlighted)>.content.actions>.primary-action-bar { background: none!important }
.monaco-workbench.no-workspace>.part.statusbar,.monaco-workbench > .part.statusbar {background: rgb(21,21,21)}
.monaco-workbench > .part.statusbar>.statusbar-item{opacity: 0.6}
.monaco-workbench>.part.panel>.title { border-top-color: rgba(255,255,255,0.1) }
.monaco-editor.vs-dark .git-dirty-modified-diff-glyph { background: none }
.monaco-editor.vs-dark .current-line {border: none}
.monaco-editor.vs-dark .selectionHighlight { background: rgba(173,214,255,0.1)}

.token.haskell { font-feature-settings: "XHS_" 1; }
.token.patel { font-feature-settings: "XPTL" 1; }

.explorer-open-editors .editor-group { visibility: hidden }
.explorer-open-editors .monaco-tree-row.expanded { margin-bottom: -22px }
.explorer-open-editors .monaco-tree-row + .monaco-tree-row.expanded { border-top: 1px solid rgba(255,255,255,0.05); margin-top: -1px }
```
