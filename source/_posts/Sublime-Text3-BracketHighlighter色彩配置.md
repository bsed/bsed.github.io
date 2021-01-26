---
title: "Sublime Text3 BracketHighlighter色彩配置"
categories: [ "Css" ]
tags: [ "Sublime Text3","brackethighlighter","配置" ]
draft: false
slug: "text3-brackethighlighter-sublime-color-configuration"
date: "2014-03-04 08:59:00"
---

通过Package Control，搜索BracketHighlighter安装即可。
![color.jpg][1]

Sublime Text3 BracketHighlighter色彩配置
BracketHighlighter安装

通过Package Control，搜索BracketHighlighter安装即可。
color

BracketHighlighter配置

Pregerence > Package Settings > BracketHighlighter > Bracket Settings – User

对应的关系：
{} － curly
() － round
[] － square
<> － angle
“” ” － quote

style: solid、underline、outline、highlight


<!--more-->


    {
        // Define region highlight styles
        "bracket_styles": {
            // "default" and "unmatched" styles are special
            // styles. If they are not defined here,
            // they will be generated internally with
            // internal defaults.
    
            // "default" style defines attributes that
            // will be used for any style that does not
            // explicitly define that attribute.  So if
            // a style does not define a color, it will
            // use the color from the "default" style.
            "default": {
                "icon": "dot",
                // BH1's original default color for reference
                "color": "entity.name.class",
                "color": "brackethighlighter.default",
                "style": "highlight"
            },
    
            // This particular style is used to highlight
            // unmatched bracekt pairs.  It is a special
            // style.
            "unmatched": {
                "icon": "question",
                "color": "brackethighlighter.unmatched",
                "style": "highlight"
            },
            // User defined region styles
            "curly": {
                "icon": "curly_bracket",
                "color": "brackethighlighter.curly",
                "style": "underline"
            },
            "round": {
                "icon": "round_bracket",
                "color": "brackethighlighter.round",
                "style": "underline"
            },
            "square": {
                "icon": "square_bracket",
                "color": "brackethighlighter.square",
                "style": "underline"
            },
            "angle": {
                "icon": "angle_bracket",
                "color": "brackethighlighter.angle",
                "style": "underline"
            },
            "tag": {
                "icon": "tag",
                "color": "brackethighlighter.tag",
                "style": "highlight"
            },
            "single_quote": {
                "icon": "single_quote",
                "color": "brackethighlighter.quote",
                "style": "underline"
            },
            "double_quote": {
                "icon": "double_quote",
                "color": "brackethighlighter.quote",
                "style": "underline"
            },
            "regex": {
                "icon": "regex",
                "color": "brackethighlighter.quote",
                "style": "outline"
            }
        },
    
    }

找到Sublime text3安装目录下的Packages中的Color Scheme – Default.sublime-package 类试D:\Program Files\Sublime Text 3\Packages\Color Scheme – Default.sublime-package

添加后缀名Color Scheme – Default.sublime-package.zip，解压找到Monokai.tmTheme，拉出来修改。
![20140304090158.png][2]

    <dict>
        <key>name</key>
        <string>Bracket Default</string>
        <key>scope</key>
        <string>brackethighlighter.default</string>
        <key>settings</key>
        <dict>
            <key>foreground</key>
            <string>#FFFFFF</string>
            <key>background</key>
            <string>#A6E22E</string>
        </dict>
    </dict>
    
    <dict>
        <key>name</key>
        <string>Bracket Unmatched</string>
        <key>scope</key>
        <string>brackethighlighter.unmatched</string>
        <key>settings</key>
        <dict>
            <key>foreground</key>
            <string>#FFFFFF</string>
            <key>background</key>
            <string>#FF0000</string>
        </dict>
    </dict>
    
    <dict>
        <key>name</key>
        <string>Bracket Curly</string>
        <key>scope</key>
        <string>brackethighlighter.curly</string>
        <key>settings</key>
        <dict>
            <key>foreground</key>
            <string>#FF00FF</string>
        </dict>
    </dict>
    
    <dict>
        <key>name</key>
        <string>Bracket Round</string>
        <key>scope</key>
        <string>brackethighlighter.round</string>
        <key>settings</key>
        <dict>
            <key>foreground</key>
            <string>#E7FF04</string>
        </dict>
    </dict>
    
    <dict>
        <key>name</key>
        <string>Bracket Square</string>
        <key>scope</key>
        <string>brackethighlighter.square</string>
        <key>settings</key>
        <dict>
            <key>foreground</key>
            <string>#FE4800</string>
        </dict>
    </dict>
    
    <dict>
        <key>name</key>
        <string>Bracket Angle</string>
        <key>scope</key>
        <string>brackethighlighter.angle</string>
        <key>settings</key>
        <dict>
            <key>foreground</key>
            <string>#02F78E</string>
        </dict>
    </dict>
    
    <dict>
        <key>name</key>
        <string>Bracket Tag</string>
        <key>scope</key>
        <string>brackethighlighter.tag</string>
        <key>settings</key>
        <dict>
            <key>foreground</key>
            <string>#FFFFFF</string>
            <key>background</key>
            <string>#0080FF</string>
        </dict>
    </dict>
    
    <dict>
        <key>name</key>
        <string>Bracket Quote</string>
        <key>scope</key>
        <string>brackethighlighter.quote</string>
        <key>settings</key>
        <dict>
            <key>foreground</key>
            <string>#56FF00</string>
        </dict>
    </dict>

将上边的代码添加到Monokai.tmTheme中，和上边的dict并列排即可。
然后再降修改完成的文件放到压缩包Color Scheme – Default.sublime-package.zip里边，完事后改名Color Scheme – Default.sublime-package放回D:\Program Files\Sublime Text 3\Packages。
**附件下载：**[Color Scheme - Default.zip][3]


  [1]: https://imgs.gnux.cn/usr/uploads/2015/08/2269400054.jpg
  [2]: https://imgs.gnux.cn/usr/uploads/2015/08/240604051.png
  [3]: https://imgs.gnux.cn/usr/uploads/2015/08/1571578027.zip