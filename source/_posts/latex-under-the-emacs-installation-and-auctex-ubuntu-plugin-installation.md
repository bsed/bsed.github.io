---
title: Ubuntu下latex安装以及emacs的auctex插件安装
date: 2014-07-13 20:44:00
updated: 2016-01-28 20:45:21
tags: 
- rails
- model
- rspec
categories: 
- rails

---
# Latex环境的安装
## 安装latex
命令安装完整的latex（接近两个G）

```bash
sudo apt-get install texlive-full
```
    
## 安装CJK包
然后安装CJK相关的包了，CJK是Chinese，Japanese，Korean三种文字的支持
 


<!--more-->


   
```bash
sudo apt-get install latex-cjk-all
```
    
## 测试latex的中文了
在tex文件中输入如下内容
    
```tex
\documentclass{article}
\usepackage{CJKutf8}
\begin{document}
p\begin{CJK}{UTF8}{gkai}
这是一个楷体中文测试，处理简体字。
\end{CJK}
\begin{CJK}{UTF8}{gbsn}
这是一个宋体中文测试，处理简体字。
\end{CJK}
\begin{CJK}{UTF8}{bkai}
這是一個big5編碼的楷體中文測試，處理繁體文字。
\end{CJK}
\begin{CJK}{UTF8}{bsmi}
這是一個个big5編碼的明體中文測試，處理繁體文字。
\end{CJK}
\end{document}
```

然后编译预览就可以了。要注意的的是现在只支持四种中文字体即`gkai`、`gbsn`、`bkai`、`bsmi`，后两种是繁体字类型，如果要加入其他的字体需要自己手动安装，这个以后再说吧。

## 测试xelatex
还有一种方法，就是使用xelatex编译，可以直接使用自带的中文字体。使用xelatex的时候为了方便使用中文字体排版，可以像以下这样配置：

```tex
\documentclass[12pt,a4paper,xetex]{moderncv}
\usepackage{fontspec,xunicode,xltxtra} % 加载 xetex 的宏包
\defaultfontfeatures{Scale=MatchLowercase} % 这个参数保证 serif、sans-serif 和 monospace 字体在小写时大小匹配
\setmainfont{Ubuntu} 
\setsansfont{Ubuntu} 
\setmonofont{Source Code Pro}
% 以下是中文字体的设置
\usepackage[slantfont,boldfont,CJKnumber]{xeCJK} % 加载 xeCJK，允许斜体、粗体和 CJK 数字
\setCJKmainfont{Microsoft YaHei UI}
\setCJKsansfont{Microsoft YaHei UI}
\setCJKmonofont{Source Code Pro}
\setCJKfamilyfont{song}{SimSun}
\setCJKfamilyfont{kai}{KaiTi}
\setCJKfamilyfont{hei}{SimHei}
\setCJKfamilyfont{fs}{FangSong}
\setCJKfamilyfont{yahei}{Microsoft YaHei}
\setCJKfamilyfont{monaco}{Monaco}
\newcommand\song{\CJKfamily{song}}
\newcommand\kai{\CJKfamily{kai}}
\newcommand\hei{\CJKfamily{hei}}
\newcommand\fs{\CJKfamily{fs}}
\newcommand\yahei{\CJKfamily{yahei}}
\newcommand\monaco{\CJKfamily{monaco}}
\newcommand{\erhao}{\fontsize{22pt}{\baselineskip}\selectfont}
\newcommand{\xiaoerhao}{\fontsize{18pt}{\baselineskip}\selectfont}
\newcommand{\sanhao}{\fontsize{16pt}{\baselineskip}\selectfont}
\newcommand{\xiaosanhao}{\fontsize{15pt}{\baselineskip}\selectfont}
\newcommand{\sihao}{\fontsize{14pt}{\baselineskip}\selectfont}
\newcommand{\xiaosihao}{\fontsize{12pt}{\baselineskip}\selectfont}
\newcommand{\wuhao}{\fontsize{10.5pt}{\baselineskip}\selectfont}
\newcommand{\xiaowuhao}{\fontsize{9pt}{\baselineskip}\selectfont}
\newcommand{\liuhao}{\fontsize{7.5pt}{\baselineskip}\selectfont}
```

## 遇到的问题
* TTF的字体可以正常的使用，但是OTF字体，比如思源黑体，无法正确的渲染