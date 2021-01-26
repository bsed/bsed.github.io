---
title: "浅析Python中__new__函数"
categories: [ "Python" ]
tags: [ "python" ]
draft: false
slug: "analysis-of-new-function-in-python"
date: "2016-10-25 20:54:00"
---

Python是面向对象的程序设计语言，Python把所有东西都被视作对象。

Python中有一类特殊的方法，即魔术方法-`Magic Method`（带两个__的方法），如`__new__`、`__init__`。

要想理解`__new__`，我们还要搞懂`__init__`。

`__init__`方法是在初始化一个类实例的时候调用，但`__init__`其实并不是实例化一个类的时候第一个被调用的方法，最先被调用的方法其实是`__new__`方法。

__new__这个方法负责创建类实例，而__init__负责初始化类实例。__new__函数可以用来自定义对象的创建，它的第一个参数是这个类的引用，然后是一些构造参数；返回值通常是对象实例的引用。


<!--more-->


常见的类的声明和实例化：

```python
class Foo(object):
    def __init__(self, a, b):
        print("__init__")
        self.a = a
        self.b = b

    def bar(self):
        pass

i = Foo(2, 3)
```
使用__new__：

```python
class Foo(object):
    def __new__(cls, *args, **kwargs):
        print("__new__")
        instance = super(Foo, cls).__new__(cls, *args, **kwargs)
        # 或instance = object.__new__(cls, *args, **kwargs)

        return instance  # 如果返回的不是本对象实例，__init__函数将不会自动调用，这时你需要手动调用

    def __init__(self, a, b):
        print("__init__")
        self.a = a
        self.b = b

    def bar(self):
        pass

i = Foo(2, 3)
```

# __new__的应用

一般情况下我们并不需要覆写__new__函数，但是写API的时候可能会用到。

## 使用__new__的单例模式

```python
class Singleton(object):
    _instance = None  # 保存实例的引用
    
    def __new__(cls, *args, **kwargs):
        if not cls._instance:
            cls._instance = object.__name__(cls, *args, **kwargs)
        return cls._instance
```
## 限制一个类所能创建的实例对象的总个数

```python
class LimitedInstances(object):
    _instances = []  # 保存创建的实例
    limit = 5   # 这个类只能创建5个对象

    def __new__(cls, *args, **kwargs):
        if not len(cls._instances) <= cls.limit:
            raise RuntimeError, "Can not create instance. Limit %s reached" % cls.limit
        instance = object.__name__(cls, *args, **kwargs)
        cls._instances.append(instance)
        return instance
    
    def __del__(self):
        # 从_instances中移除实例
        self._instance.remove(self)

```

## 自定义实例对象

```python
class CustomizeInstance(object):

    def __new__(cls, a, b):
        if not createInstance():
            raise RuntimeError, "Count not create instance"
        instance = super(CustomizeInstance, cls).__new__(cls, a, b)
        instance.a = a
        return instance

    def __init__(self, a, b):
        pass
```

```python
class AbstractClass(object):
 
    def __new__(cls, a, b):
        instance = super(AbstractClass, cls).__new__(cls)
        instance.__init__(a, b)
        return 3
 
    def __init__(self, a, b):
        print "Initializing Instance", a, b
 
# >>> a = AbstractClass(2, 3)
# Initializing Instance 2 3
# >>> a 
# 3

```

**总结**：不到万不得已不要使用这个方法，Zen of Python有这样一句：”Simple is better than complex.”

```
>>> import this
The Zen of Python, by Tim Peters

Beautiful is better than ugly.
Explicit is better than implicit.
Simple is better than complex.
Complex is better than complicated.
Flat is better than nested.
Sparse is better than dense.
Readability counts.
Special cases aren't special enough to break the rules.
Although practicality beats purity.
Errors should never pass silently.
Unless explicitly silenced.
In the face of ambiguity, refuse the temptation to guess.
There should be one-- and preferably only one --obvious way to do it.
Although that way may not be obvious at first unless you're Dutch.
Now is better than never.
Although never is often better than *right* now.
If the implementation is hard to explain, it's a bad idea.
If the implementation is easy to explain, it may be a good idea.
Namespaces are one honking great idea -- let's do more of those!
>>>
```
