---
title: "django-uuslug: 方便的将中文转化成拼音 slug 的工具"
categories: [ "Python" ]
tags: [ "django","uuslug" ]
draft: false
slug: "djangouuslug-convenient-tool-for-converting-chinese-into-pinyin-slug"
date: "2015-10-17 12:40:00"
---

作者: [Desmond Chen](https://plus.google.com/108316084942604297544?rel=author), 发布日期: 2014-01-22, 修改日期: 2014-01-22
## 总体介绍

django-uuslug是一个很方便的将中文转化成拼音slug的插件. 通过使用django-uuslug, 可以保确保slug是唯一的, 并且都是unicode编码的.

django中的slug是什么: slug是一个新闻属性, django中的slug是指有效URL的一部分, 能使URL更加清晰易懂. 比如有这样一篇文章, 标题是"那个13岁的孩子", 它的URL地址是"/news/na-ge-13-sui-de-hai-zi", 后面这一部分便是slug.

## 如何安装

使用VirtualEnv在Bash shell中:

    pip install django-uuslug


<!--more-->


## 如何使用

### 将任意编码转为相应的unicode:

    from uuslug import slugify
    
    txt = "This is a test ---"
    r = slugify(txt)
    self.assertEquals(r, "this-is-a-test")
    
    txt = "This -- is a ## test ---"
    r = slugify(txt)
    self.assertEquals(r, "this-is-a-test")
    
    txt = 'C\'est déjà l\'été.'
    r = slugify(txt)
    self.assertEquals(r, "cest-deja-lete")
    
    txt = '你好我是中国人'
    r = slugify(txt)
    self.assertEquals(r, "nin-hao-wo-shi-zhong-guo-ren")
    
    txt = 'Компьютер'
    r = slugify(txt)
    self.assertEquals(r, "kompiuter")
    
    txt = 'jaja---lol-méméméoo--a'
    r = slugify(txt)
    self.assertEquals(r, "jaja-lol-mememeoo-a")
    
    txt = 'jaja---lol-méméméoo--a'
    r = slugify(txt, max_length=9)
    self.assertEquals(r, "jaja-lol")
    
    txt = 'jaja---lol-méméméoo--a'
    r = slugify(txt, max_length=15)
    self.assertEquals(r, "jaja-lol-mememe")
    
    txt = 'jaja---lol-méméméoo--a'
    r = slugify(txt, max_length=50)
    self.assertEquals(r, "jaja-lol-mememeoo-a")
    
    txt = 'jaja---lol-méméméoo--a'
    r = slugify(txt, max_length=15, word_boundary=True)
    self.assertEquals(r, "jaja-lol-a")
    
    txt = 'jaja---lol-méméméoo--a'
    r = slugify(txt, max_length=19, word_boundary=True)
    self.assertEquals(r, "jaja-lol-mememeoo")
    
    txt = 'jaja---lol-méméméoo--a'
    r = slugify(txt, max_length=20, word_boundary=True)
    self.assertEquals(r, "jaja-lol-mememeoo-a")
    
    txt = 'jaja---lol-méméméoo--a'
    r = slugify(txt, max_length=20, word_boundary=True, separator=".")
    self.assertEquals(r, "jaja.lol.mememeoo.a")
    
    txt = 'jaja---lol-méméméoo--a'
    r = slugify(txt, max_length=20, word_boundary=True, separator="ZZZZZZ")
    self.assertEquals(r, "jajaZZZZZZlolZZZZZZmememeooZZZZZZa")

### b. 唯一性测试, 重写model.py中的save()方法:

    from django.db import models
    from uuslug import uuslug
    
    class CoolSlug(models.Model):
        name = models.CharField(max_length=100)
        slug = models.CharField(max_length=200)
    
        def __unicode__(self):
            return self.name
    
        def save(self, *args, **kwargs):
            # self.slug = uuslug(self.name, instance=self, separator="_") # optional non-dash separator
            self.slug = uuslug(self.name, instance=self)
            super(CoolSlug, self).save(*args, **kwargs)

### c. 指定起始数字:

        self.slug = uuslug(self.name, instance=self, start_no=2)
        
        # 第二个slug就会以-2结尾
        name = "john"
        c = CoolSlug.objects.create(name=name)
        c.save()
        print c.slug # => "john"
        
        c1 = CoolSlug.objects.create(name=name)
        c1.save()
        print c1.slug # => "john-1"
        
        c2 = CoolSlug.objects.create(name=name)
        c2.save()
        print c2.slug # => "john-2"
        d. 限制slug的长短:
        
        class SmartTruncatedSlug(models.Model):
            name = models.CharField(max_length=19)
            slug = models.CharField(max_length=19)
        
            def __unicode__(self):
                return self.name
        
            def save(self, *args, **kwargs):
                self.slug = uuslug(self.name, instance=self, start_no=9, max_length=19, word_boundary=True)
                super(SmartTruncatedSlug, self).save(*args, **kwargs)
    
    # 测试
    name = 'jaja---lol-méméméoo--a'
    
    obj = SmartTruncatedSlug.objects.create(name=name)
    print obj.slug # "jaja-lol-mememeoo"  --- where 19 is max_length (first slug, no duplicate yet)
    
    obj = SmartTruncatedSlug.objects.create(name=name)
    print obj.slug # "jaja-lol-mememeoo-9" --- where 19 is max_length, start_no = 9
    
    obj = SmartTruncatedSlug.objects.create(name=name)
    print obj.slug # "jaja-lol-mememeo-10" -- where 19 is max_length, smart appending "-10"

## 4. 其他资源

最后给出该项目的Github地址: [django-uuslug](https://github.com/un33k/django-uuslug), 方便各位自己查看.
