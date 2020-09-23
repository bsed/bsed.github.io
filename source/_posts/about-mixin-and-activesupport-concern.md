---
title:  关于Mixin和ActiveSupport::Concern
date: 2015-11-15 20:49:00
updated: 2016-01-28 20:50:19
tags: 
- css
- html
- block
categories: 
- css

---
##ruby对象模型

实际上ActiveSupport::Concern是对mixin的一些通用的模式进行的一个封装。而mixin就像是一个“虚拟的类”，用来注入到class或者module的祖先链中去。

```ruby
module MyMod
end

class Base
end

class Child < Base
  include MyMod
end

# irb> Child.ancestors
#  => [Child, MyMod, Base, Object, Kernel, BasicObject]
```

上面的情况与下面这段代码有着相同的祖先链结构

```ruby
class Base
end

class MyMod < Base
end

class Child < MyMod
end

# irb> Child.ancestors
#  => [Child, MyMod, Base, Object, Kernel, BasicObject]
```

模型也可以用来作为扩展一个对象

```ruby
my_obj = Object.new
my_obj.extend MyMod

# irb> my_obj.singleton_class.ancestors
#  => [MyMod, Object, Kernel, BasicObject]
```

在Ruby中，每个对象都有一个eigenclass，Object#extend的行为与Module#include的行为是相同的，只是前者是作用在对象的eigenclass上面。所以下面的代码和上面的代码效果一致。


<!--more-->


```ruby
my_obj = Object.new
my_obj.singleton_class.class_eval do
  include MyMod
end

# irb> my_obj.singleton_class.ancestors
#  => [MyMod, Object, Kernel, BasicObject]
```

这就是ruby中静态方法和类方法实现的方式。实习上，ruby中并没有像java中那样的静态方法和类方法。只有在类的eigenclass中的普通方法。下面两种情况的的行为是相同的。

```ruby
class MyClass
  extend MyMod
end

# irb> MyClass.singleton_class.ancestors
#  => [MyMod, Class, Module, Object, Kernel, BasicObject]

class MyClass
  class << self
    include MyMod
  end
end

# irb> MyClass.singleton_class.ancestors
#  => [MyMod, Class, Module, Object, Kernel, BasicObject]
```

实际上在ruby中类也是对象。

## Mixin

ruby提供了一些钩子方法，当module被混入到类和模块中的时候，这些钩子方法就会被调用，比如

```ruby
module MyMod
  def self.included(base)
    base.send(:include, InstanceMethods)
    base.extend ClassMethods
    base.class_eval do
      a_class_method
    end
  end

  module InstanceMethods
    def an_instance_method
    end
  end

  module ClassMethods
    def a_class_method
      puts "a_class_method called"
    end
  end
end

# irb> class MyClass
# irb>   include MyMod
# irb> end
# a_class_method called
end

# irb> MyClass.ancestors
#  => [MyClass, MyMod::InstanceMethods, MyMod, Object, Kernel, BasicObject]
# irb> MyClass.singleton_class.ancestors
#  => [MyMod::ClassMethods, Class, Module, Object, Kernel, BasicObject]
```

一个单独的模块添加了实例方法、“类方法”以及直接在base类中执行它（此例中名为a_class_method)。

##ActiveSupport::Concern

下面是用ActiveSupport::concern重写的代码：

```ruby
module MyMod
  extend ActiveSupport::Concern

  included do
    a_class_method
  end

  def an_instance_method
  end

  module ClassMethods
    def a_class_method
      puts "a_class_method called"
    end
  end
end
```

其中嵌套的`InstanceMethods`被移除了，`an_instance_method()`被直接定义在module中。因为在MyClass的祖先链中（[MyClass, MyMod::InstanceMethods, MyMod, Object, Kernel]），并不需要`MyMod::InstanceMethods方法，因为MyMod中的方法已经存在在祖先链中了。

同时ActiveSupport::Concern也去掉了一些之前样板模式中的一些代码：不再需要定义 included钩子了，也不再需要自己去 extend base类中的 ClassMethods了，也不需要手动调用 class_eval了。

### 惰性求值（lazy evaluation）

ActiveSupport::Concern还提供了一个叫做懒惰求值的特性（lazy evaluation）。先来看下普通的mixin

```ruby
module MyModA
end

module MyModB
  include MyModA
end

class MyClass
  include MyModB
end

# irb> MyClass.ancestors
#  => [MyClass, MyModB, MyModA, Object, Kernel, BasicObject]
```

如果想要在想要在included的时候在base类中做一些特别的事情，如

```ruby
module MyModA
  def self.included(base)
    base.class_eval do
      has_many :squirrels
    end
  end
end
```

这时候，MyModA会被MyModBinclud，included()中的代码将会运行，如果has_many在MyModB中还未定义的话，那么就会报错。

```ruby
irb :050 > module MyModB
irb :051?>   include MyModA
irb :052?> end
NoMethodError: undefined method `has_many' for MyModB:Module
 from (irb):46:in `included'
 from (irb):45:in `class_eval'
 from (irb):45:in `included'
 from (irb):51:in `include'
 ```
 
 而在ActiveSupport::Concern中，将会延迟所有included钩子，只到模块被一个非ActiveSupport::Concern包含。
 
 ```ruby
 module MyModA
  extend ActiveSupport::Concern

  included do
    has_many :squirrels
  end
end

module MyModB
  extend ActiveSupport::Concern
  include MyModA
end

class MyClass
  def self.has_many(*args)
    puts "has_many(#{args.inspect}) called"
  end

  include MyModB
end
# irb>
# has_many([:squirrels]) called


# irb> MyClass.ancestors
#  => [MyClass, MyModB, MyModA, Object, Kernel, BasicObject]
# irb> MyClass.singleton_class.ancestors
#  => [Class, Module, Object, Kernel, BasicObject]
```

###模块依赖

最后ActiveSupport::concern还能够很好的解决模块依赖关系，如果给两个模块Foo和Bar，其中Bar依赖与Foo，一般都会这么写

```ruby
module Foo
  def self.included(base)
    base.class_eval do
      def self.method_injected_by_foo
        ...
      end
    end
  end
end

module Bar
  def self.included(base)
    base.method_injected_by_foo
  end
end

class Host
  include Foo # We need to include this dependency for Bar
  include Bar # Bar is the module that Host really needs
end
```

但是，host为啥要关心Bar的依赖关系呀？照理说应该这样才符合懒人的习惯

```ruby
module Bar
  include Foo
  def self.included(base)
    base.method_injected_by_foo
  end
end

class Host
  include Bar
end
```

不幸的是，这样是会报错的。当Foo被include的时候，他的base是Bar模块而不是Host类。而如果使用ActiveSupport::Concern的话，模块的依赖关系就能够很好的解决啦

```ruby
require 'active_support/concern'

module Foo
  extend ActiveSupport::Concern
  included do
    def self.method_injected_by_foo
      ...
    end
  end
end

module Bar
  extend ActiveSupport::Concern
  include Foo

  included do
    self.method_injected_by_foo
  end
end

class Host
  include Bar # works, Bar takes care now of its dependencies
end
```


##参考资料
* [Concerning Yourself with ActiveSupport::Concern](http://www.fakingfantastic.com/2010/09/20/concerning-yourself-with-active-support-concern/)
* [rails手册中的ActiveSupport::Concern](http://api.rubyonrails.org/classes/ActiveSupport/Concern.html)
* [How Ruby method dispatch works](https://blog.jcoglan.com/2013/05/08/how-ruby-method-dispatch-works)