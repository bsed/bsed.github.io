---
title: 如何在 Gimp、Inkscape下导入自定义Palettes 调色板
date: 2019-04-13 20:10:00
updated: 2019-04-13 21:17:46
tags: 
- Symbols
categories: 
- default

---
现在在google 大佬的带领下 提倡使用 material, flat, social 的 界面UX设计，毫无疑问，设计中最重要的一环是颜色，选择正确的颜色有助于提高增强设计。错误的颜色选择对用户来说是糟糕的设计。有太多的组合，对设计新手来说是很那控制的。

## Flat Design 颜色选择
啥是扁平设计呢？ 通常，平面设计是剥离任何三维元素的设计。
![Flat design trend.jpg][1]

Flat扁平化设计是一种几件的UI设计类型，通常用在图形用户界面，如 web应用程序和移动应用程序。特别是在图形材料中作为海报、艺术设计、文件、出版物等。


<!--more-->


## Material设计颜色选择
Material Design 是Google开发的一种设计。2014年6月25日，2014年Google I/O大会上宣布 Material Design。Material Design可以更自由地使用基于网格的布局，响应动画和过渡，填充以及光照和阴影等深度效果。
![Material design trend.jpg][2]

Google的大部分Android移动应用程序都采用了这种设计，包括Gmail，YouTube，Google云端硬盘，Google文档，表格和幻灯片，Google地图以及所有Google Play品牌的应用程序。

## Flat、Material、Social 颜色代码
首先，复制以下颜色代码并粘贴到您喜欢的文本编辑器。然后用.gpl扩展名保存。

### Flat Design
```
GIMP Palette
Name: Flat UI Colors

#
26 188 156 Turquoise
22 160 133 Green Sea
46 204 113 Emerland
39 174 96 Nephritis
52 152 219 Peter River
41 128 185 Belize Hole
155 89 182 Amethyst
142 68 173 Wisteria
52 73 94 Wet Asphalt
44 62 80 Midnight Blue
241 196 15  Sun Flower
243 156 18  Orange
230 126 34 Carrot
211 84 0 Pumpkin
231 76 60 Alizarin
192 57 43 Pomegranate
236 240 241 Clouds
189 195 199 Silver
149 165 166 Concrete
127 140 141 Asbestos
```

### Material Design

```
GIMP Palette
Name: Material UI Colors

#
255 248 225  amber 50
255 236 179  amber 100
255 224 130  amber 200
255 213  79  amber 300
255 202  40  amber 400
255 193   7  amber 500
255 179   0  amber 600
255 160   0  amber 700
255 143   0  amber 800
255 111   0  amber 900
255 229 127  amber a100
255 215  64  amber a200
255 196   0  amber a400
255 171   0  amber a700
227 242 253  blue 50
187 222 251  blue 100
144 202 249  blue 200
100 181 246  blue 300
 66 165 245  blue 400
 33 150 243  blue 500
 30 136 229  blue 600
 25 118 210  blue 700
 21 101 192  blue 800
 13  71 161  blue 900
130 177 255  blue a100
 68 138 255  blue a200
 41 121 255  blue a400
 41  98 255  blue a700
224 247 250  cyan 50
178 235 242  cyan 100
128 222 234  cyan 200
 77 208 225  cyan 300
 38 198 218  cyan 400
  0 188 212  cyan 500
  0 172 193  cyan 600
  0 151 167  cyan 700
  0 131 143  cyan 800
  0  96 100  cyan 900
132 255 255  cyan a100
 24 255 255  cyan a200
  0 229 255  cyan a400
  0 184 212  cyan a700
251 233 231  deep-orange 50
255 204 188  deep-orange 100
255 171 145  deep-orange 200
255 138 101  deep-orange 300
255 112  67  deep-orange 400
255  87  34  deep-orange 500
244  81  30  deep-orange 600
230  74  25  deep-orange 700
216  67  21  deep-orange 800
191  54  12  deep-orange 900
255 158 128  deep-orange a100
255 110  64  deep-orange a200
255  61   0  deep-orange a400
221  44   0  deep-orange a700
237 231 246  deep-purple 50
209 196 233  deep-purple 100
179 157 219  deep-purple 200
149 117 205  deep-purple 300
126  87 194  deep-purple 400
103  58 183  deep-purple 500
 94  53 177  deep-purple 600
 81  45 168  deep-purple 700
 69  39 160  deep-purple 800
 49  27 146  deep-purple 900
179 136 255  deep-purple a100
124  77 255  deep-purple a200
101  31 255  deep-purple a400
 98   0 234  deep-purple a700
232 245 233  green 50
200 230 201  green 100
165 214 167  green 200
129 199 132  green 300
102 187 106  green 400
 76 175  80  green 500
 67 160  71  green 600
 56 142  60  green 700
 46 125  50  green 800
 27  94  32  green 900
185 246 202  green a100
105 240 174  green a200
  0 230 118  green a400
  0 200  83  green a700
232 234 246  indigo 50
197 202 233  indigo 100
159 168 218  indigo 200
121 134 203  indigo 300
 92 107 192  indigo 400
 63  81 181  indigo 500
 57  73 171  indigo 600
 48  63 159  indigo 700
 40  53 147  indigo 800
 26  35 126  indigo 900
140 158 255  indigo a100
 83 109 254  indigo a200
 61  90 254  indigo a400
 48  79 254  indigo a700
225 245 254  light-blue 50
179 229 252  light-blue 100
129 212 250  light-blue 200
 79 195 247  light-blue 300
 41 182 246  light-blue 400
  3 169 244  light-blue 500
  3 155 229  light-blue 600
  2 136 209  light-blue 700
  2 119 189  light-blue 800
  1  87 155  light-blue 900
128 216 255  light-blue a100
 64 196 255  light-blue a200
  0 176 255  light-blue a400
  0 145 234  light-blue a700
241 248 233  light-green 50
220 237 200  light-green 100
197 225 165  light-green 200
174 213 129  light-green 300
156 204 101  light-green 400
139 195  74  light-green 500
124 179  66  light-green 600
104 159  56  light-green 700
 85 139  47  light-green 800
 51 105  30  light-green 900
204 255 144  light-green a100
178 255  89  light-green a200
118 255   3  light-green a400
100 221  23  light-green a700
249 251 231  lime 50
240 244 195  lime 100
230 238 156  lime 200
220 231 117  lime 300
212 225  87  lime 400
205 220  57  lime 500
192 202  51  lime 600
175 180  43  lime 700
158 157  36  lime 800
130 119  23  lime 900
244 255 129  lime a100
238 255  65  lime a200
198 255   0  lime a400
174 234   0  lime a700
255 243 224  orange 50
255 224 178  orange 100
255 204 128  orange 200
255 183  77  orange 300
255 167  38  orange 400
255 152   0  orange 500
251 140   0  orange 600
245 124   0  orange 700
239 108   0  orange 800
230  81   0  orange 900
255 209 128  orange a100
255 171  64  orange a200
255 145   0  orange a400
255 109   0  orange a700
252 228 236  pink 50
248 187 208  pink 100
244 143 177  pink 200
240  98 146  pink 300
236  64 122  pink 400
233  30  99  pink 500
216  27  96  pink 600
194  24  91  pink 700
173  20  87  pink 800
136  14  79  pink 900
255 128 171  pink a100
255  64 129  pink a200
245   0  87  pink a400
197  17  98  pink a700
243 229 245  purple 50
225 190 231  purple 100
206 147 216  purple 200
186 104 200  purple 300
171  71 188  purple 400
156  39 176  purple 500
142  36 170  purple 600
123  31 162  purple 700
106  27 154  purple 800
 74  20 140  purple 900
234 128 252  purple a100
224  64 251  purple a200
213   0 249  purple a400
170   0 255  purple a700
255 235 238  red 50
255 205 210  red 100
239 154 154  red 200
229 115 115  red 300
239  83  80  red 400
244  67  54  red 500
229  57  53  red 600
211  47  47  red 700
198  40  40  red 800
183  28  28  red 900
255 138 128  red a100
255  82  82  red a200
255  23  68  red a400
213   0   0  red a700
224 242 241  teal 50
178 223 219  teal 100
128 203 196  teal 200
 77 182 172  teal 300
 38 166 154  teal 400
  0 150 136  teal 500
  0 137 123  teal 600
  0 121 107  teal 700
  0 105  92  teal 800
  0  77  64  teal 900
167 255 235  teal a100
100 255 218  teal a200
 29 233 182  teal a400
  0 191 165  teal a700
255 253 231  yellow 50
255 249 196  yellow 100
255 245 157  yellow 200
255 241 118  yellow 300
255 238  88  yellow 400
255 235  59  yellow 500
253 216  53  yellow 600
251 192  45  yellow 700
249 168  37  yellow 800
245 127  23  yellow 900
255 255 141  yellow a100
255 255   0  yellow a200
255 234   0  yellow a400
255 214   0  yellow a700
236 239 241  blue-grey 50
207 216 220  blue-grey 100
176 190 197  blue-grey 200
144 164 174  blue-grey 300
120 144 156  blue-grey 400
 96 125 139  blue-grey 500
 84 110 122  blue-grey 600
 69  90 100  blue-grey 700
 55  71  79  blue-grey 800
 38  50  56  blue-grey 900
239 235 233  brown 50
215 204 200  brown 100
188 170 164  brown 200
161 136 127  brown 300
141 110  99  brown 400
121  85  72  brown 500
109  76  65  brown 600
 93  64  55  brown 700
 78  52  46  brown 800
 62  39  35  brown 900
250 250 250  grey 50
245 245 245  grey 100
238 238 238  grey 200
224 224 224  grey 300
189 189 189  grey 400
158 158 158  grey 500
117 117 117  grey 600
 97  97  97  grey 700
 66  66  66  grey 800
 33  33  33  grey 900
  0   0   0     black
255 255 255 white
```

## Social Design 

```
GIMP Palette
Name: Social UI Colors

#
0 172 237 twitter
59 89 152 facebook
221 75 57 googleplus
203 32 39 pinterest
0 123 182 linkedin
187 0 0  youtube
170 212 80 vimeo
50 80 109 tumblr
81 127 164 instagram
255 0 132 flickr
234 76 137 dribbble
168 36 0 quora
0 114 177 foursquare
91 154 104 forrst
69 102 142 vk
33 117 155 wordpress
235 72 35 stumbleupon
123 0 153 yahoo
251 143 61 blogger
255 58 0 soundcloud

```

## 将调色板.gpl文件 导入到 GImp、Inkscape中

### 将 Flat, Material, and Social UI 调色板 导入到Gimp 中

打开Gimp应用程序: Windows → Dockable Dialogs → Palettes;
右键打开 `Import Palette`， 看到" Import a New Palette " 窗口，找到(Palette file) 位置，选择调色板文件，然后找到.gpl文件导入。

![gimp_import_palette.png][3]
### 将 Flat, Material, and Social UI 调色板 导入到Inkscape 中

将flat.gpl, material,gpl,social.gpl 复制到 .config/inkscape/palettes/中。或者打开终端然后运行一下命令。
```bash
cp [path]/*.gpl .config/inkscape/palettes/
```


附件下载：
[Palletes Flat, Material, Social UI .zip][4]


  [1]: https://imgs.gnux.cn/usr/uploads/2019/04/3972056631.jpg
  [2]: https://imgs.gnux.cn/usr/uploads/2019/04/3539555041.jpg
  [3]: https://imgs.gnux.cn/usr/uploads/2019/04/1855545493.png
  [4]: https://imgs.gnux.cn/usr/uploads/2019/04/2245303503.zip