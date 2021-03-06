---
title: "Golang绘图技术（image/draw包介绍）"
categories: [ "Golang" ]
tags: [ "golang","image/draw" ]
draft: false
slug: "473"
date: "2012-12-06 13:19:00"
---

 image/draw 包仅仅定义了一个操作：通过可选的蒙版图（mask image）
 把一个原始图片绘制到目标图片上，这个操作是出奇的灵活，可以优雅和高效的执行很多常见的图像处理任务。

    // Draw calls DrawMask with a nil mask.
    func Draw(dst Image, r image.Rectangle, src image.Image, sp image.Point, op Op)
    func DrawMask(dst Image, r image.Rectangle, src image.Image, sp image.Point,
    mask image.Image, mp image.Point, op Op)
第一个函数Draw是没有使用蒙版mask的调用方法，它内部其实就是调用的mask为 nil的方法。

它的参数描述如下：

 - dst  绘图的背景图。
 - r 是背景图的绘图区域
 - src 是要绘制的图
 - sp 是 src 对应的绘图开始点（绘制的大小 r变量定义了）
 - mask 是绘图时用的蒙版，控制替换图片的方式。
 - mp 是绘图时蒙版开始点（绘制的大小 r变量定义了）
 - op Op is a Porter-Duff compositing operator.  参考文章：http://blog.csdn.net/ison81/article/details/5468763
 - Porter-Duff 等式12种规则可以看这篇博客：
http://www.blogjava.net/onedaylover/archive/2008/01/16/175675.html
 
下图就是几个相关的例子：

mask 蒙版是渐变
![26164651-39417a27a4354e20809962a5860b9636.png][1]
#### 给一个矩形填充颜色 ####
使用 Draw方法的逻辑效果图：

代码：

    m := image.NewRGBA(image.Rect(0, 0, 640, 480))
    blue := color.RGBA{0, 0, 255, 255}
    draw.Draw(m, m.Bounds(), &image.Uniform{blue}, image.ZP, draw.Src)

#### 拷贝图片的一部分 ####
使用 Draw方法的逻辑效果图
![26164652-13c0e0c4aec241c5a75810e004830898.png][2]
相关代码：

    r := image.Rectangle{dp, dp.Add(sr.Size())}  // 获得更换区域
    draw.Draw(dst, r, src, sr.Min, draw.Src)

如果是复制整个图片，则更简单：

    sr = src.Bounds()         // 获取要复制图片的尺寸
    r := sr.Sub(sr.Min).Add(dp)   // 目标图的要剪切区域
    draw.Draw(dst, r, src, sr.Min, draw.Src)

#### 图片滚动效果 ####
效果如下图:
![26164654-e0648f2436ea4288b582852631849cd6.png][3]
假设我们需要把图片 m 上移20个像素.

相关代码:

    b := m.Bounds()
    p := image.Pt(0, 20)
    // Note that even though the second argument is b,
    // the effective rectangle is smaller due to clipping.
    draw.Draw(m, b, m, b.Min.Add(p), draw.Src)
    dirtyRect := b.Intersect(image.Rect(b.Min.X, b.Max.Y-20, b.Max.X, b.Max.Y))

#### 把一个图片转成RGBA格式 ####
效果图:
![26164657-36679138bd3f446c8c142c13c6abb326.png][4]
相关代码:

    b := src.Bounds()
    m := image.NewRGBA(b)
    raw.Draw(m, b, src, b.Min, draw.Src)

通过蒙版画特效
效果图
![26164659-efcfe1d8174c4ae4926941ce8ae77d1d.png][5]
相关代码
注意,一个image对象只需要实现下面几个就可,这也就是Go接口强大的地方.

    type Image interface {
       // ColorModel returns the Image's color model.
        ColorModel() color.Model
        // Bounds returns the domain for which At can return non-zero color.
         // The bounds do not necessarily contain the point (0, 0).
         Bounds() Rectangle
         // At returns the color of the pixel at (x, y).
        // At(Bounds().Min.X, Bounds().Min.Y) returns the upper-left pixel of the grid.
         // At(Bounds().Max.X-1, Bounds().Max.Y-1) returns the lower-right one.
         At(x, y int) color.Color
     }

#### 画一个字体 ####
效果图，画一个蓝色背景的字体。
![26164702-897cf38e4e504cb981500b6df27f09c1.png][6]
相关伪代码：

    src := &image.Uniform{color.RGBA{0, 0, 255, 255}}
    mask := theGlyphImageForAFont()
    mr := theBoundsFor(glyphIndex)
    draw.DrawMask(dst, mr.Sub(mr.Min).Add(p), src, image.ZP, mask, mr.Min, draw.Over)
     
上面例子完整的代码请看：

[http://golang.org/doc/progs/image_draw.go](http://golang.org/doc/progs/image_draw.go)

参考：

 [http://blog.golang.org/go-imagedraw-package](http://blog.golang.org/go-imagedraw-package)
文章来源：http://www.cnblogs.com/ghj1976/p/3443638.html


  [1]: https://imgs.gnux.cn/usr/uploads/2014/11/1858444158.png
  [2]: https://imgs.gnux.cn/usr/uploads/2014/11/3498819125.png
  [3]: https://imgs.gnux.cn/usr/uploads/2014/11/3110338395.png
  [4]: https://imgs.gnux.cn/usr/uploads/2014/11/379701284.png
  [5]: https://imgs.gnux.cn/usr/uploads/2014/11/4290880931.png
  [6]: https://imgs.gnux.cn/usr/uploads/2014/11/3324201967.png