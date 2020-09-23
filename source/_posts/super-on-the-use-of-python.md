---
title: Python的super用法研究
date: 2013-09-25 15:55:00
updated: 2015-02-25 15:58:11
tags: 
- 软件
categories: 
- go

---
从学习笔记中扒拉出来的。
一、问题的发现与提出

　　在Python类的方法（method）中，要调用父类的某个方法，在Python 2.2以前，通常的写法如代码段1：

 代码段1：

 class A:
  def __init__(self):
   print "enter A"
   print "leave A"

 class B(A):
  def __init__(self):
   print "enter B"
   A.__init__(self)
   print "leave B"

 >>> b = B()

 enter B
 enter A
 leave A
 leave B

即，使用非绑定的类方法（用类名来引用的方法），并在参数列表中，引入待绑定的对象（self），从而达到调用父类的目的。


<!--more-->


　　这样做的缺点是，当一个子类的父类发生变化时（如类B的父类由A变为C时），必须遍历整个类定义，把所有的通过非绑定的方法的类名全部替换过来，例如代码段2，

 代码段2：

 class B(C):    # A --> C
  def __init__(self):
   print "enter B"
   C.__init__(self) # A --> C
   print "leave B"

如果代码简单，这样的改动或许还可以接受。但如果代码量庞大，这样的修改可能是灾难性的。

　　因此，自Python 2.2开始，Python添加了一个关键字super，来解决这个问题。下面是Python 2.3的官方文档说明：

 super(type[, object-or-type])

  Return the superclass of type. If the second argument is omitted the super object
  returned is unbound. If the second argument is an object, isinstance(obj, type) 
  must be true. If the second argument is a type, issubclass(type2, type) must be 
  true. super() only works for new-style classes.

  A typical use for calling a cooperative superclass method is:

   class C(B):
       def meth(self, arg):
           super(C, self).meth(arg)

  New in version 2.2.

　　从说明来看，可以把类B改写如代码段3：

 代码段3：

 class A(object):    # A must be new-style class
  def __init__(self):
   print "enter A"
   print "leave A"

 class B(C):     # A --> C
  def __init__(self):
   print "enter B"
   super(B, self).__init__()
   print "leave B"

　　尝试执行上面同样的代码，结果一致，但修改的代码只有一处，把代码的维护量降到最低，是一个不错的用法。因此在我们的开发过程中，super关键字被大量使用，而且一直表现良好。

　　在我们的印象中，对于super(B, self).__init__()是这样理解的：super(B, self)首先找到B的父类（就是类A），然后把类B的对象self转换为类A的对象（通过某种方式，一直没有考究是什么方式，惭愧），然后“被转换”的类A对象调用自己的__init__函数。考虑到super中只有指明子类的机制，因此，在多继承的类定义中，通常我们保留使用类似代码段1的方法。

　　有一天某同事设计了一个相对复杂的类体系结构（我们先不要管这个类体系设计得是否合理，仅把这个例子作为一个题目来研究就好），代码如代码段4：

 代码段4：

 class A(object):
  def __init__(self):
   print "enter A"
   print "leave A"

 class B(object):
  def __init__(self):
   print "enter B"
   print "leave B"

 class C(A):
  def __init__(self):
   print "enter C"
   super(C, self).__init__()
   print "leave C"

 class D(A):
  def __init__(self):
   print "enter D"
   super(D, self).__init__()
   print "leave D"
 class E(B, C):
  def __init__(self):
   print "enter E"
   B.__init__(self)
   C.__init__(self)
   print "leave E"

 class F(E, D):
  def __init__(self):
   print "enter F"
   E.__init__(self)
   D.__init__(self)
   print "leave F"

 >>> f = F()

 enter F
 enter E
 enter B
 leave B
 enter C
 enter D
 enter A
 leave A
 leave D
 leave C
 leave E
 enter D
 enter A
 leave A
 leave D
 leave F

　　明显地，类A和类D的初始化函数被重复调用了2次，这并不是我们所期望的结果！我们所期望的结果是最多只有类A的初始化函数被调用2次——其实这是多继承的类体系必须面对的问题。我们把代码段4的类体系画出来，如下图：

    object
   |       /
   |        A
   |      / |
   B  C  D
    /   /   |
      E    |
        /   |
          F

　　按我们对super的理解，从图中可以看出，在调用类C的初始化函数时，应该是调用类A的初始化函数，但事实上却调用了类D的初始化函数。好一个诡异的问题！

二、走进Python的源码世界

　　我们尝试改写代码段4中的函数调用，但都没有得到我们想要的结果，这不得不使我们开始怀疑：我们对super的理解是否出了问题。

　　我们重新阅读了Python的官方文档，正如您所见，官方文档并没有详细的原理说明。到网络上去搜索，确实有人发现了同样的问题，并在一些论坛中讨论，但似乎并没有实质性的解答。既然，没有前人的足迹，我们只好走进Python的源码世界，去追溯问题的根源。

　　我们考查的是Python 2.3的源码（估计Python 2.4的源码可能也差不多）。首先，搜索关键字"super"。唯一找到的是bltinmodule.c中的一句：

 SETBUILTIN("super",  &PySuper_Type);

　　于是，我们有了对super的第一个误解：super并非是一个函数，而是一个类（PySuper_Type）。

　　在typeobject.c中找到了PySuper_Type的定义：

 代码段5：

 PyTypeObject PySuper_Type = {
  PyObject_HEAD_INIT(&PyType_Type)
  0,     /* ob_size */
  "super",    /* tp_name */
  sizeof(superobject),   /* tp_basicsize */
  0,     /* tp_itemsize */
  /* methods */
  super_dealloc,     /* tp_dealloc */
  0,     /* tp_print */
  0,     /* tp_getattr */
  0,     /* tp_setattr */
  0,     /* tp_compare */
  super_repr,    /* tp_repr */
  0,     /* tp_as_number */
  0,     /* tp_as_sequence */
  0,            /* tp_as_mapping */
  0,     /* tp_hash */
  0,     /* tp_call */
  0,     /* tp_str */
  super_getattro,    /* tp_getattro */
  0,     /* tp_setattro */
  0,     /* tp_as_buffer */
  Py_TPFLAGS_DEFAULT | Py_TPFLAGS_HAVE_GC |
   Py_TPFLAGS_BASETYPE,  /* tp_flags */
   super_doc,    /* tp_doc */
   super_traverse,    /* tp_traverse */
   0,     /* tp_clear */
  0,     /* tp_richcompare */
  0,     /* tp_weaklistoffset */
  0,     /* tp_iter */
  0,     /* tp_iternext */
  0,     /* tp_methods */
  super_members,    /* tp_members */
  0,     /* tp_getset */
  0,     /* tp_base */
  0,     /* tp_dict */
  super_descr_get,   /* tp_descr_get */
  0,     /* tp_descr_set */
  0,     /* tp_dictoffset */
  super_init,    /* tp_init */
  PyType_GenericAlloc,   /* tp_alloc */
  PyType_GenericNew,   /* tp_new */
  PyObject_GC_Del,          /* tp_free */
 };

　　从代码段5中可以得知，super类只改写了几个方法，最主要的包括：tp_dealloc，tp_getattro，tp_traverse，tp_init。

　　再看superobject的定义：

 代码段6：

 typedef struct {
  PyObject_HEAD
  PyTypeObject *type;
  PyObject *obj;
  PyTypeObject *obj_type;
 } superobject;

　　从代码段6中可以看到superobject的数据成员仅有3个指针（3个对象的引用）。要知道这3个对象分别代表什么，则必需考查super_init的定义：

 代码段7：

 static int
 super_init(PyObject *self, PyObject *args, PyObject *kwds)
 {
  superobject *su = (superobject *)self;
  PyTypeObject *type;
  PyObject *obj = NULL;
  PyTypeObject *obj_type = NULL;
 
  if (!PyArg_ParseTuple(args, "O!|O:super", &PyType_Type, &type, &obj))
   return -1;
  if (obj == Py_None)
   obj = NULL;
  if (obj != NULL) {
   obj_type = supercheck(type, obj);
   if (obj_type == NULL)
    return -1;
   Py_INCREF(obj);
  }
  Py_INCREF(type);
  su->type = type;
  su->obj = obj;
  su->obj_type = obj_type;
  return 0;
 }

　　从代码中可以看到，super_init首先通过PyArg_ParseTuple把传入的参数列表解释出来，分别放在type和obj变量之中。然后通过supercheck测试可选参数obj是否合法，并获得实例obj的具体类类型。最后，把type, obj和obj_type记录下来。也就是说，super对象只是简单作了一些记录，并没有作任何转换操作。

　　查找问题的切入点是为什么在类C中的super调用会切换到类D的初始化函数。于是在super_init中添加条件断点，并跟踪其后的Python代码。最终进入到super_getattro函数——对应于super对象访问名字__init__时的搜索操作。

 代码段8（省略部分无关代码，并加入一些注释）：

 static PyObject *
 super_getattro(PyObject *self, PyObject *name)
 {
  superobject *su = (superobject *)self;
  int skip = su->obj_type == NULL;
  ……
  if (!skip) {
   PyObject *mro, *res, *tmp, *dict;
   PyTypeObject *starttype;
   descrgetfunc f;
   int i, n;

   starttype = su->obj_type;  // 获得搜索的起点：super对象的obj_type
   mro = starttype->tp_mro;  // 获得类的mro
   ……
   for (i = 0; i < n; i++) {  // 搜索mro中，定位mro中的type
    if ((PyObject *)(su->type) == PyTuple_GET_ITEM(mro, i))
     break;
   }
   i++;       // 切换到mro中的下一个类
   res = NULL;
   for (; i < n; i++) {   // 在mro以后的各个命名空间中搜索指定名字
    tmp = PyTuple_GET_ITEM(mro, i);
    if (PyType_Check(tmp))
     dict = ((PyTypeObject *)tmp)->tp_dict;
    else if (PyClass_Check(tmp))
     dict = ((PyClassObject *)tmp)->cl_dict;
    else
     continue;
    res = PyDict_GetItem(dict, name);
    if (res != NULL) {
     Py_INCREF(res);
     f = res->ob_type->tp_descr_get;
     if (f != NULL) {
      tmp = f(res, su->obj,
       (PyObject *)starttype);
      Py_DECREF(res);
      res = tmp;
     }
     return res;
    }
   }
  }
  return PyObject_GenericGetAttr(self, name);
 }

　　从代码中可以看出，super对象在搜索命名空间时，其实是基于类实例的mro进行。那么什么是mro呢？查找官方文档，有：

 PyObject* tp_mro 
  Tuple containing the expanded set of base types, starting with the type itself and 
  ending with object, in Method Resolution Order.

  This field is not inherited; it is calculated fresh by PyType_Ready().

　　也就是说，mro中记录了一个类的所有基类的类类型序列。查看mro的记录，发觉包含7个元素，7个类名分别为：

 F E B C D A object

　　从而说明了为什么在C.__init__中使用super(C, self).__init__()会调用类D的初始化函数了。

　　我们把代码段4改写为：

 代码段9：

 class A(object):
  def __init__(self):
   print "enter A"
   super(A, self).__init__()  # new
   print "leave A"

 class B(object):
  def __init__(self):
   print "enter B"
   super(B, self).__init__()  # new
   print "leave B"

 class C(A):
  def __init__(self):
   print "enter C"
   super(C, self).__init__()
   print "leave C"

 class D(A):
  def __init__(self):
   print "enter D"
   super(D, self).__init__()
   print "leave D"
 class E(B, C):
  def __init__(self):
   print "enter E"
   super(E, self).__init__()  # change
   print "leave E"

 class F(E, D):
  def __init__(self):
   print "enter F"
   super(F, self).__init__()  # change
   print "leave F"

 >>> f = F()

 enter F
 enter E
 enter B
 enter C
 enter D
 enter A
 leave A
 leave D
 leave C
 leave B
 leave E
 leave F

　　明显地，F的初始化不仅完成了所有的父类的调用，而且保证了每一个父类的初始化函数只调用一次。

三、延续的讨论

　　我们再重新看上面的类体系图，如果把每一个类看作图的一个节点，每一个从子类到父类的直接继承关系看作一条有向边，那么该体系图将变为一个有向图。不能发现mro的顺序正好是该有向图的一个拓扑排序序列。

　　从而，我们得到了另一个结果——Python是如何去处理多继承。支持多继承的传统的面向对象程序语言（如C++）是通过虚拟继承的方式去实现多继承中父类的构造函数被多次调用的问题，而Python则通过mro的方式去处理。

　　但这给我们一个难题：对于提供类体系的编写者来说，他不知道使用者会怎么使用他的类体系，也就是说，不正确的后续类，可能会导致原有类体系的错误，而且这样的错误非常隐蔽的，也难于发现。

四、小结

　　1. super并不是一个函数，是一个类名，形如super(B, self)事实上调用了super类的初始化函数，
       产生了一个super对象；
　　2. super类的初始化函数并没有做什么特殊的操作，只是简单记录了类类型和具体实例；
　　3. super(B, self).func的调用并不是用于调用当前类的父类的func函数；
　　4. Python的多继承类是通过mro的方式来保证各个父类的函数被逐一调用，而且保证每个父类函数
       只调用一次（如果每个类都使用super）；
　　5. 混用super类和非绑定的函数是一个危险行为，这可能导致应该调用的父类函数没有调用或者一
       个父类函数被调用多次。

原文：[http://blog.csdn.net/johnsonguo/article/details/585193](http://blog.csdn.net/johnsonguo/article/details/585193)