---
title: "xelatex+xeCJK在ubuntu Linux中显示中文"
categories: [ "Linux" ]
tags: [ "pandoc","ubuntu" ]
draft: false
slug: "xelatexxecjk-display-in-linux-ubuntu"
date: "2014-02-18 12:43:00"
---

xelatex+xeCJK在ubuntu Linux中显示中文
=====================================

## pandoc 

### 安装pandoc 

#### 安装ghc

port安装ghc
`sudo port install ghc`

homebrew安装ghc
`brew install ghc`

#### 编译cabal 
Common Architecture for Building Applications and Libraries2
Cabal是Haskell用于构建应用程序和库的公共架构。
简单的来说它是haskell的包管理，和上面的port、brew 命令类似。


<!--more-->


下载地址 [http://haskell.org/cabal/](http://haskell.org/cabal/) 
解压后
```
ghc --make Setup.hs
    ./Setup configure
    ./Setup build 
    sudo ./Setup install
```
#### 安装cabal 
安装之前我们需要更新
`cabal update`

##### 安装pandoc

`cabal install pandoc`

##### 添加到PATH

`export PATH=/Users/fdhuang/.cabal/bin:$PATH`

markdown 转doc

`pandoc iot.md -o iot.docx`

##### markdown 转pdf 
这里只针对于安装了mactex
openSUSE安装pandoc

`zypper install pandoc`

Ubuntu安装pandoc

`sudo aptitude install pandoc`

当然也可以用apt-get
安装mactex
下载地址: 
[http://mirror.ctan.org/systems/mac/mactex/mactex-basic.pkg](http://mirror.ctan.org/systems/mac/mactex/mactex-basic.pkg) 

markdown转pdf
不含中文时

`pandoc doc.pandoc doc.pdf`

含有中文时的简单作法

`pandoc --latex-engine=xelatex iot.md -o iot.pdf -V mainfont="SimSun"`

markdown转pdf 含中文

`pandoc --template=pandoc.template --latex-engine=xelatex iot.md -o iot.pdf`

也就是我们需要一个template.tex的模板用于构建，下载地址[https://raw.githubusercontent.com/tzengyuxio/pages/gh-pages/pandoc/pm-template.latex](https://raw.githubusercontent.com/tzengyuxio/pages/gh-pages/pandoc/pm-template.latex) 

或者可以参考这个

[https://github.com/gmszone/iot-doc](https://github.com/gmszone/iot-doc)

```
\documentclass[$if(fontsize)$$fontsize$,$endif$$if(lang)$$lang$,$endif$$if(papersize)$$papersize$,$endif$]{$documentclass$}
    \usepackage{geometry} 		% 設定邊界
    \geometry{
      top=1in,
      inner=1in,
      outer=1in,
      bottom=1in,
      headheight=3ex,
      headsep=2ex
    }
    \usepackage[T1]{fontenc}
    \usepackage{lmodern}
    \usepackage{amssymb,amsmath}
    \usepackage{ifxetex,ifluatex}
    \usepackage{fixltx2e} % provides \textsubscript
    % use upquote if available, for straight quotes in verbatim environments
    \IfFileExists{upquote.sty}{\usepackage{upquote}}{}
    \ifnum 0\ifxetex 1\fi\ifluatex 1\fi=0 % if pdftex
      \usepackage[utf8]{inputenc}
    $if(euro)$
      \usepackage{eurosym}
    $endif$
    \else % if luatex or xelatex
      \usepackage{fontspec} 	% 允許設定字體
      \usepackage{xeCJK} 		% 分開設置中英文字型
      \setCJKmainfont{STSong} 	% 設定中文字型
      \setmainfont{Georgia} 	% 設定英文字型
      \setromanfont{Georgia} 	% 字型
      \setmonofont{Courier New}
      \linespread{1.2}\selectfont 	% 行距
      \XeTeXlinebreaklocale "zh" 	% 針對中文自動換行
      \XeTeXlinebreakskip = 0pt plus 1pt % 字與字之間加入0pt至1pt的間距，確保左右對整齊
      \parindent 0em 		% 段落縮進
      \setlength{\parskip}{20pt} 	% 段落之間的距離
      \ifxetex
        \usepackage{xltxtra,xunicode}
      \fi
      \defaultfontfeatures{Mapping=tex-text,Scale=MatchLowercase}
      \newcommand{\euro}{€}
    $if(mainfont)$
        \setmainfont{$mainfont$}
    $endif$
    $if(sansfont)$
        \setsansfont{$sansfont$}
    $endif$
    $if(monofont)$
        \setmonofont{$monofont$}
    $endif$
    $if(mathfont)$
        \setmathfont{$mathfont$}
    $endif$
    \fi
    % use microtype if available
    \IfFileExists{microtype.sty}{\usepackage{microtype}}{}
    $if(geometry)$
    \usepackage[$for(geometry)$$geometry$$sep$,$endfor$]{geometry}
    $endif$
    $if(natbib)$
    \usepackage{natbib}
    \bibliographystyle{plainnat}
    $endif$
    $if(biblatex)$
    \usepackage{biblatex}
    $if(biblio-files)$
    \bibliography{$biblio-files$}
    $endif$
    $endif$
    $if(listings)$
    \usepackage{listings}
    $endif$
    $if(lhs)$
    \lstnewenvironment{code}{\lstset{language=Haskell,basicstyle=\small\ttfamily}}{}
    $endif$
    $if(highlighting-macros)$
    $highlighting-macros$
    $endif$
    $if(verbatim-in-note)$
    \usepackage{fancyvrb}
    $endif$
    $if(tables)$
    \usepackage{longtable}
    $endif$
    $if(graphics)$
    \usepackage{graphicx}
    % We will generate all images so they have a width \maxwidth. This means
    % that they will get their normal width if they fit onto the page, but
    % are scaled down if they would overflow the margins.
    \makeatletter
    \def\maxwidth{\ifdim\Gin@nat@width>\linewidth\linewidth
    \else\Gin@nat@width\fi}
    \makeatother
    \let\Oldincludegraphics\includegraphics
    \renewcommand{\includegraphics}[1]{\Oldincludegraphics[width=\maxwidth]{#1}}
    $endif$
    \ifxetex
      \usepackage[setpagesize=false, % page size defined by xetex
                  unicode=false, % unicode breaks when used with xetex
                  xetex]{hyperref}
    \else
      \usepackage[unicode=true]{hyperref}
    \fi
    \hypersetup{breaklinks=true,
                bookmarks=true,
                pdfauthor={$author-meta$},
                pdftitle={$title-meta$},
                colorlinks=true,
                urlcolor=$if(urlcolor)$$urlcolor$$else$blue$endif$,
                linkcolor=$if(linkcolor)$$linkcolor$$else$magenta$endif$,
                pdfborder={0 0 0}}
    \urlstyle{same}  % don't use monospace font for urls
    $if(links-as-notes)$
    % Make links footnotes instead of hotlinks:
    \renewcommand{\href}[2]{#2\footnote{\url{#1}}}
    $endif$
    $if(strikeout)$
    \usepackage[normalem]{ulem}
    % avoid problems with \sout in headers with hyperref:
    \pdfstringdefDisableCommands{\renewcommand{\sout}{}}
    $endif$
    \setlength{\parindent}{0pt}
    %\setlength{\parskip}{6pt plus 2pt minus 1pt}
    \setlength{\emergencystretch}{3em}  % prevent overfull lines


    \title{\huge 在OSX平台上的XeLaTeX中文測試} % 設置標題，使用巨大字體
    \author{FoolEgg.com} 		% 設置作者
    \date{February 2013} 		% 設置日期
    \usepackage{titling}
    \setlength{\droptitle}{-8em} 	% 將標題移動至頁面的上面


    \usepackage{fancyhdr}
    \usepackage{lastpage}
    \pagestyle{fancyplain}


    $if(numbersections)$
    \setcounter{secnumdepth}{5}
    $else$
    \setcounter{secnumdepth}{0}
    $endif$
    $if(verbatim-in-note)$
    \VerbatimFootnotes % allows verbatim text in footnotes
    $endif$
    $if(lang)$
    \ifxetex
      \usepackage{polyglossia}
      \setmainlanguage{$mainlang$}
    \else
      \usepackage[$lang$]{babel}
    \fi
    $endif$
    $for(header-includes)$
    $header-includes$
    $endfor$


    $if(title)$
    \title{$title$}
    $endif$
    \author{$for(author)$$author$$sep$ \and $endfor$}
    \date{$date$}


    \begin{document}
    $if(title)$
    \maketitle
    $endif$


    $for(include-before)$
    $include-before$


    $endfor$
    $if(toc)$
    {
    \hypersetup{linkcolor=black}
    \setcounter{tocdepth}{$toc-depth$}
    \tableofcontents
    }
    $endif$
    $body$


    $if(natbib)$
    $if(biblio-files)$
    $if(biblio-title)$
    $if(book-class)$
    \renewcommand\bibname{$biblio-title$}
    $else$
    \renewcommand\refname{$biblio-title$}
    $endif$
    $endif$
    \bibliography{$biblio-files$}


    $endif$
    $endif$
    $if(biblatex)$
    \printbibliography$if(biblio-title)$[title=$biblio-title$]$endif$


    $endif$
    $for(include-after)$
    $include-after$


    $endfor$
    \end{document}
```
详细可见下文，主要是因为tex对中文支持的缘故 

也可以在这里获取一个[pandoc template pandoc 毕业设计模板](http://www.phodal.com/blog/pandoc-template-tex-pandoc/)

## textlive

### 安装texlive（如果空间足够，最好装完整版）之后安装xelatex

`sudo apt-get install texlive-xetex`

apt-cache search cjk，找到相关宏包，安装

`sudo apt-get install latex-cjk-xcjk cjk-latex latex-cjk-chinese`

### 安装喜欢的中文字体

`sudo apt-get install xfonts-wqy ttf-wqy-microhei ttf-wqy-zenhei`

### 安装英文字体

`#~ apt-cache search 'courier new'`

ttf-mscorefonts-installer - Installer for Microsoft TrueType core fonts

`#~ sudo apt-get install ttf-mscorefonts-installer`

#### 用fc-list查看已安装的所有字体，用fc-cache刷新字体缓存

把wiki上的文件改一下：（[http://linux-wiki.cn/wiki/zh-hans/LaTeX%E4%B8%AD%E6%96%87%E6%8E%92%E7%89%88%EF%BC%88%E4%BD%BF%E7%94%A8XeTeX%EF%BC%89](http://linux-wiki.cn/wiki/zh-hans/LaTeX%E4%B8%AD%E6%96%87%E6%8E%92%E7%89%88%EF%BC%88%E4%BD%BF%E7%94%A8XeTeX%EF%BC%89)）

```
\documentclass[$if(fontsize)$$fontsize$,$endif$$if(lang)$$lang$,$endif$$if(papersize)$$papersize$,$endif$]{$documentclass$}
\usepackage{geometry} 		% 設定邊界
\geometry{
  top=1in,
  inner=1in,
  outer=1in,
  bottom=1in,
  headheight=3ex,
  headsep=2ex
}
\usepackage[T1]{fontenc}
\usepackage{lmodern}
\usepackage{amssymb,amsmath}
\usepackage{ifxetex,ifluatex}
\usepackage{fixltx2e} % provides \textsubscript
% use upquote if available, for straight quotes in verbatim environments
\IfFileExists{upquote.sty}{\usepackage{upquote}}{}
\ifnum 0\ifxetex 1\fi\ifluatex 1\fi=0 % if pdftex
  \usepackage[utf8]{inputenc}
$if(euro)$
  \usepackage{eurosym}
$endif$
\else % if luatex or xelatex
  \usepackage{fontspec} 	% 允許設定字體
  \usepackage{xeCJK} 		% 分開設置中英文字型
  \setCJKmainfont{LiHei Pro} 	% 設定中文字型
  \setmainfont{Georgia} 	% 設定英文字型
  \setromanfont{Georgia} 	% 字型
  \setmonofont{Courier New}
  \linespread{1.2}\selectfont 	% 行距
  \XeTeXlinebreaklocale "zh" 	% 針對中文自動換行
  \XeTeXlinebreakskip = 0pt plus 1pt % 字與字之間加入0pt至1pt的間距，確保左右對整齊
  \parindent 0em 		% 段落縮進
  \setlength{\parskip}{20pt} 	% 段落之間的距離
  \ifxetex
    \usepackage{xltxtra,xunicode}
  \fi
  \defaultfontfeatures{Mapping=tex-text,Scale=MatchLowercase}
  \newcommand{\euro}{€}
$if(mainfont)$
    \setmainfont{$mainfont$}
$endif$
$if(sansfont)$
    \setsansfont{$sansfont$}
$endif$
$if(monofont)$
    \setmonofont{$monofont$}
$endif$
$if(mathfont)$
    \setmathfont{$mathfont$}
$endif$
\fi
% use microtype if available
\IfFileExists{microtype.sty}{\usepackage{microtype}}{}
$if(geometry)$
\usepackage[$for(geometry)$$geometry$$sep$,$endfor$]{geometry}
$endif$
$if(natbib)$
\usepackage{natbib}
\bibliographystyle{plainnat}
$endif$
$if(biblatex)$
\usepackage{biblatex}
$if(biblio-files)$
\bibliography{$biblio-files$}
$endif$
$endif$
$if(listings)$
\usepackage{listings}
$endif$
$if(lhs)$
\lstnewenvironment{code}{\lstset{language=Haskell,basicstyle=\small\ttfamily}}{}
$endif$
$if(highlighting-macros)$
$highlighting-macros$
$endif$
$if(verbatim-in-note)$
\usepackage{fancyvrb}
$endif$
$if(tables)$
\usepackage{longtable}
$endif$
$if(graphics)$
\usepackage{graphicx}
% We will generate all images so they have a width \maxwidth. This means
% that they will get their normal width if they fit onto the page, but
% are scaled down if they would overflow the margins.
\makeatletter
\def\maxwidth{\ifdim\Gin@nat@width>\linewidth\linewidth
\else\Gin@nat@width\fi}
\makeatother
\let\Oldincludegraphics\includegraphics
\renewcommand{\includegraphics}[1]{\Oldincludegraphics[width=\maxwidth]{#1}}
$endif$
\ifxetex
  \usepackage[setpagesize=false, % page size defined by xetex
              unicode=false, % unicode breaks when used with xetex
              xetex]{hyperref}
\else
  \usepackage[unicode=true]{hyperref}
\fi
\hypersetup{breaklinks=true,
            bookmarks=true,
            pdfauthor={$author-meta$},
            pdftitle={$title-meta$},
            colorlinks=true,
            urlcolor=$if(urlcolor)$$urlcolor$$else$blue$endif$,
            linkcolor=$if(linkcolor)$$linkcolor$$else$magenta$endif$,
            pdfborder={0 0 0}}
\urlstyle{same}  % don't use monospace font for urls
$if(links-as-notes)$
% Make links footnotes instead of hotlinks:
\renewcommand{\href}[2]{#2\footnote{\url{#1}}}
$endif$
$if(strikeout)$
\usepackage[normalem]{ulem}
% avoid problems with \sout in headers with hyperref:
\pdfstringdefDisableCommands{\renewcommand{\sout}{}}
$endif$
\setlength{\parindent}{0pt}
%\setlength{\parskip}{6pt plus 2pt minus 1pt}
\setlength{\emergencystretch}{3em}  % prevent overfull lines

\title{\huge 在OSX平台上的XeLaTeX中文測試} % 設置標題，使用巨大字體
\author{FoolEgg.com} 		% 設置作者
\date{February 2013} 		% 設置日期
\usepackage{titling}
\setlength{\droptitle}{-8em} 	% 將標題移動至頁面的上面

\usepackage{fancyhdr}
\usepackage{lastpage}
\pagestyle{fancyplain}

$if(numbersections)$
\setcounter{secnumdepth}{5}
$else$
\setcounter{secnumdepth}{0}
$endif$
$if(verbatim-in-note)$
\VerbatimFootnotes % allows verbatim text in footnotes
$endif$
$if(lang)$
\ifxetex
  \usepackage{polyglossia}
  \setmainlanguage{$mainlang$}
\else
  \usepackage[$lang$]{babel}
\fi
$endif$
$for(header-includes)$
$header-includes$
$endfor$

$if(title)$
\title{$title$}
$endif$
\author{$for(author)$$author$$sep$ \and $endfor$}
\date{$date$}

\begin{document}
$if(title)$
\maketitle
$endif$

$for(include-before)$
$include-before$

$endfor$
$if(toc)$
{
\hypersetup{linkcolor=black}
\setcounter{tocdepth}{$toc-depth$}
\tableofcontents
}
$endif$
$body$

$if(natbib)$
$if(biblio-files)$
$if(biblio-title)$
$if(book-class)$
\renewcommand\bibname{$biblio-title$}
$else$
\renewcommand\refname{$biblio-title$}
$endif$
$endif$
\bibliography{$biblio-files$}

$endif$
$endif$
$if(biblatex)$
\printbibliography$if(biblio-title)$[title=$biblio-title$]$endif$

$endif$
$for(include-after)$
$include-after$

$endfor$
\end{document}



```

#### 再用xelatex编译，

`xelatex filename.tex`



