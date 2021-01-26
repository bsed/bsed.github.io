---
title: "使用GIMP软件制作简单的 GIF动画"
categories: [ "设计" ]
tags: [ "gimp" ]
draft: false
slug: "use-gimp-software-to-create-a-simple-gif-animation"
date: "2017-05-07 07:53:00"
---

方法/步骤
1、打开素材图像文件。
文件-->打开，打开一个图像文件；文件-->作为图层打开，打开另一个图像文件。先后打开的两个文件，在 GIMP 的当前工作文件中成为两个图层。


<!--more-->


注：有很多方法将现有图像导入成图层，比如鼠标拖放就很简单易用。
![gimp2.8_gif_01.png][1]
2、保存文件。
文件-->保存，取名保存为 xcf 格式的文件，以备反复修改之用。
注：xcf 格式文件是 GIMP 的工作文件，它保存了图像的所有信息，即使在导出、关闭后也可以再次打开，然后对图层、通道、颜色等元素进行修改和再次导出。类似于 PhotoShop 的 psd 文件。
![gimp2.8_gif_02.png][2]
3、导出文件。
文件-->导出为…，在弹出的 导出图像 对话框-->名称 输入框中，直接将文件名后缀改为 gif，然后按对话框右下角的导出按钮。
注：GIMP 在导出图像时，需要手动更改导出文件的后缀。不能只在导出对话框里选择文件格式。
![gimp2.8_gif_03.png][3]
4、设定动画参数。
在接下来出现的 图像导出为GIF 对话框中，选中 作为动画 复选框，然后设定是否循环、帧间延时和帧间替换等参数。
![gimp2.8_gif_04.png][4]
5、打开动画文件。
在目标文件夹中双击导出的文件，即可查看做好的 GIF 动画了。
几乎所有的浏览器都支持 GIF 格式的图像文件，如图即为上述过程产生的动画文件，被导入到本经验中。
![gimp2.8_gif_05.gif][5]
6、为动画增加特效。
GIF 动画虽然不能像 flash 动画那样特效很多，但也绝不限于如第 5 步骤中生成的那样简单。在第 1 步骤中的导入的图层，可以对它们进行复制、加入滤镜、设置替换时间等操作，让导出的动画有更多特效，更好看。如图为加入了过度效果的动画。
![gimp2.8_mb_06.png][6]

原文: https://jingyan.baidu.com/article/19192ad815e90ee53e57070d.html

  [1]: https://imgs.gnux.cn/usr/uploads/2017/05/3289690067.png
  [2]: https://imgs.gnux.cn/usr/uploads/2017/05/950424624.png
  [3]: https://imgs.gnux.cn/usr/uploads/2017/05/2415307518.png
  [4]: https://imgs.gnux.cn/usr/uploads/2017/05/2084351178.png
  [5]: https://imgs.gnux.cn/usr/uploads/2017/05/2322622908.gif
  [6]: https://imgs.gnux.cn/usr/uploads/2017/05/2661546117.png