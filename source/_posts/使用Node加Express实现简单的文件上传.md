---
title: "使用Node加Express实现简单的文件上传"
categories: [ "JS" ]
tags: [ "nodejs","express","文件上传" ]
draft: false
slug: "using-node-and-express-to-achieve-a-simple-file-upload"
date: "2015-08-12 21:04:00"
---

![nodejs.png][1]
几天前，我在实现一个网络应用的时候，碰到一个非常普通的需求：文件上传。这是我第一次使用Node来做文件上传，所以我通过Google查了很多资料。

不幸的是所有的文章都太老了，不是太过复杂就是解释错误，所以我如往常一样发了一封问询邮件到邮件列表中。Holowaychuk先生一如既往的用令人难以置信的速度回复了我，他的回答让我懂得首先应该做什么：读文档。

### 上传表单
这是整个挑战中最平常的一环，您可能已经非常熟悉了这一部分，无论如何，为了文章的完整性，我还是必须说一下。
你将需要使用一个表单来进行方件上传，我使用Jade来生成HTML代码，它看起来是这样的：


<!--more-->


    form(action="...", method="post", enctype="multipart/form-data")
      input(type="file", name="fileName")

`form.action`将指向一个文件上传的路由，继续往下看。

### 接收上传文件
如果你使用的是最新版本的Node和Express，那么上传文件是一块小甜饼。
在我们的上传路由里，请求参数是req.files，通过它来设置允许上传文件的类型。它看起来像下面这样：


    {
      fileName: {
        size: 11885,
        path: '/tmp/1423b4f7e0211fab48f932f33a',
        name: 'avatar.png',
        type: 'image/png',
        lastModifiedDate: Sun, 05 Feb 2012 05:31:09 GMT,
        _writeStream: {
          path: '/tmp/1423b4f7e0211fab48f932f33a',
          fd: 14,
          writable: false,
          flags: 'w',
          encoding: 'binary',
          mode: 438,
          bytesWritten: 11885,
          busy: false,
          _queue: [],
          drainable: true
        },
        length: [Getter],
        filename: [Getter],
        mime: [Getter]
      }
    }

在`req.files`对象下，file属性是你HTML里标记的表单值，req.files将为每一个有效的HTML文件表单包含一个这样的属性。
文件对象包含：类型、文件大小、名字属性，这些属性将用以在服务器端进行判断确认。

### 保存上传的文件
假设文件是有效的，下一步你将用到文件路径属性，文件一开始将保存到Tmp文件夹里，你的程序需要将文件从临时目录移到你的目标文件夹里。


    fs.readFile(req.files.fileName.path, function (err, data) {
      // ...
      var newPath = __dirname + "/uploads/uploadedFileName";
      fs.writeFile(newPath, data, function (err) {
        res.redirect("back");
      });
    });

在fs.readFile的回调函数中，我们通过文件内容获取数据属性，例子中的应用需要修改文件属性并且将文件保存到一个新的位置，所以fs.writeFile便是用以将文件数据写到一个新的路径下。
如果你的应用仅仅是要将文件保存到一个新的位置，你可以使用fs.rename来实现。

使用Node加Express上传文件大概就是这样，我曾经使用过许多服务器端的语言如Python、Java、Scala和PHP来做文件上传功能，但是我都感觉没有使用Node简单，所以我不认为Javascript应该被标记为一种劣质的服务器端语言。


  [1]: https://imgs.gnux.cn/usr/uploads/2015/08/1749490222.png