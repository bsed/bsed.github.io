---
title: "解决qt5.7下编译的 wiznote 无法输入中文"
categories: [ "Linux" ]
tags: [ "ubuntu","wiznote","qt5.7" ]
draft: false
slug: "qt57-under-the-compiler-to-solve-the-wiznote-can-not-enter-chinese"
date: "2017-03-11 20:44:00"
---

> 系统: ubuntu17.04

安装`fcitx-frontend-qt5`
`sudo apt-get install fcitx-frontend-qt5`

查看安装路径
```bash
dpkg -L fcitx-frontend-qt5
```
如图所示:
![fcitx-frontend-qt5.png][1]

找到下面路径的内容,复制到qt5.7的安装目录下
![platforminputcontexts.png][2]
```bash
/usr/lib/x86_64-linux-gnu/qt5/plugins/platforminputcontexts/libfcitxplatforminputcontextplugin.so
```
根据自己的安装情况找到该文件

将上面的文件拷贝到Qt安装的如下目录：
```bash
Qt5.7.0/5.7/gcc_64/plugins/platforminputcontexts
Qt5.7.0/Tools/QtCreator/lib/Qt/plugins/platforminputcontexts
```

```bash
cp /usr/lib/x86_64-linux-gnu/qt5/plugins/platforminputcontexts/libfcitxplatforminputcontextplugin.so /home/kelvin/Qt5.7.0/5.7/gcc_64/plugins/platforminputcontexts

cp /usr/lib/x86_64-linux-gnu/qt5/plugins/platforminputcontexts/libfcitxplatforminputcontextplugin.so /home/kelvin/Qt5.7.0/Tools/QtCreator/lib/Qt/plugins/platforminputcontexts
```
这样就可以在wiznote 使用中文了.(笔者使用的是搜狗拼音linux版本) 最终效果如下,
![wiznote_ubuntu17.04.png][3]
 {-.-}
参考:
https://my.oschina.net/lieefu/blog/505363?p=2
http://jinbitou.net/2017/01/05/2319.html
http://www.jianshu.com/p/792c9941c0e8
http://packages.ubuntu.com/zesty/amd64/fcitx-libs-dev/filelist
http://blog.csdn.net/flfihpv259/article/details/51556464
http://www.cnblogs.com/cposture/p/5397694.html
http://jishu.y5y.com.cn/flfihpv259/article/details/51556464/ export http://www.bbsmax.com/A/lk5alQemJ1/

  [1]: https://imgs.gnux.cn/usr/uploads/2017/03/3131402250.png
  [2]: https://imgs.gnux.cn/usr/uploads/2017/03/2038467409.png
  [3]: https://imgs.gnux.cn/usr/uploads/2017/03/996450994.png