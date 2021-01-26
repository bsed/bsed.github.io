---
title: "Rails中model测试的一些方法"
categories: [ "Rails" ]
tags: [ "rails","model","unittest" ]
draft: false
slug: "some-methods-of-model-test-in-rails"
date: "2015-10-13 20:48:00"
---

## 测试的哲学
* 测试应该是可靠的
* 测试应该容易书写
* 测试应该容易理解


## 一些妥协
* 速度不是测试中关注的重点
* 过于DRY的代码不是测试中关注的重点
[参考原文](http://everydayrails.com/2012/03/12/testing-series-intro.html#sthash.CilJZyad.dpuf)


## Model的Specs
1. 为当前的Model创建一个spec
2. 可以使用factories来简化测试数据的维护和创建
3. 为Model的validation，class，以及instance method写能够通过的测试用例


### get started
A model spec should include tests for the following


#### factory默认生成的对象必须合法

```ruby
describe Contact 
  it "has a valid factory" 
  it "is invalid without a firstname" 
  it "is invalid without a lastname" 
  it "returns a contact's full name as a string"
``` 

* 每一个测试用例只测试一件事情
* 每一个测试用例都必须明确
* 每一个用例的描述都以动词开头而不是should

####可以使用Factorygirl创建数据，然后使用`Faker` gem 可以用来创建随即的数据
```ruby
require 'faker' 
FactoryGirl.define do 
  factory :contact do |f| 
    f.firstname { Faker::Name.first_name } 
    f.lastname { Faker::Name.last_name } 
  end 
end 
```

#### 不能通过validation的数据，应该是不合法的
可以使用`Factory.build()`实例化一个新的model，但是并不保存它

```ruby
# spec/models/phone_spec.rb 
it "does not allow duplicate phone numbers per contact" do 
  contact = Factory(:contact) 
  Factory(:phone, contact: contact, phone_type: "home", number: "785-555-1234") 
  Factory.build(:phone, contact: contact, phone_type: "mobile", number: "785-555-1234").should_not be_valid 
end 
```

Validation的部分

```ruby
# app/models/phone.rb 
validates :phone, uniqueness: { scope: :contact_id }
```

测试validation的时候要记住，不光是测试所有数据都合法的happy path，同样还需要测试错误的条件。

#### 类和成员方法要能够按预期运行
```ruby
def name 
  [firstname, lastname].join " "  
end
```
为这个特性编写测试用例
  
```ruby
it "returns a contact's full name as a string" do 
  contact = Factory(:contact, firstname: "John", lastname: "Doe")
  contact.name.should == "John Doe" 
end 
```
#### 测试类方法

特册应该返回name以给定字母开头的contact的list

```ruby
# app/models/contact.rb 
def self.by_letter(letter) 
  where("lastname LIKE ?", "#{letter}%").order(:lastname) 
end 
```

然后测试它

```ruby
# spec/models/contact_spec.rb 
require 'spec_helper' 
describe Contact do 
  # validation examples ... 
  it "returns a sorted array of results that match" do
    smith = Factory(:contact, lastname: "Smith") 
    jones = Factory(:contact, lastname: "Jones") 
    johnson = Factory(:contact, lastname: "Johnson") 
    Contact.by_letter("J").should == [johnson, jones] 
  end 
end 
```

参考资料：
[ How I learned to test my Rails applications, Part 3: Model specs](http://everydayrails.com/2012/03/19/testing-series-rspec-models-factory-girl.html#sthash.BNN9Bvqo.dpuf)