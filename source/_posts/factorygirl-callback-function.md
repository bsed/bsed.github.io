---
title: FactoryGirl 回调函数
date: 2015-10-19 20:59:00
updated: 2016-01-28 21:00:18
tags: 
- css3
- background-size
categories: 
- css

---
factorygirl中有三种钩子函数`after_build`,`after_create`,`after_stub`，其中，当build一个object之后就会调用`after_build`这个回调函数。其他两个调用的情况类似。
## 基本的has_many的关联

Model：

```ruby
class Article < ActiveRecord::Base
  has_many :comments
end

class Comment < ActiveRecord::Base
  belongs_to :article
end
```


<!--more-->


Fatories：

```ruby
factory :article do
  body 'password'

  factory :article_with_comment do
    after_create do |article|
      create(:comment, article: article)
    end
  end
end

factory :comment do
  body 'Great article!'
end
```

然后可以这样使用回调函数了

```ruby
article = create(:article_with_comment)
```

而不是

```ruby
article = create(:article)
create(:comment, article: article)
```

## 多态关联

随着类之间的关系越来越复杂，节省的时间会越来越多

Models:

```ruby
class User < ActiveRecord::Base
  has_many :interests, as: :interested
  has_many :topics, through: :interests
end

class Interest < ActiveRecord::Base
  belongs_to :topic
  belongs_to :interested, polymorphic: true
end
```

构件的factories：

```ruby
factory :user
  email
  password 'password'

  factory :email_confirmed_user do
    email_confirmed { true }
  end
end

factory :topic do
  name 'topic_name'
end

factory :interest
  topic
  interested factory: :user
end

factory :music_interest, class: 'Interest' do
  topic.association(:topic, name: 'Music')
end

factory :sports_interest, class: 'Interest' do
  topic.association(:topic, name: 'Sports')
end
```

最后的部分：

```ruby
factory :musical_user, parent: :email_confirmed_user do
  after_create { |user| create(:music_interest, interested: user)
end

factory :sporty_user, parent: :email_confirmed_user do
  after_create { |user| create(:sports_interest, interested: user) }
end
```

所以你可以这样调用：

```ruby
user = create(:musical_user)
```

而不是

```ruby
user = create(:email_confirmed_user)
create(:music_interest, interested: user)
```


## 使用factorygirl的build_stubbed生成更快的测试用例

使用built_stubbed与使用build和create相比，可以减少很多在数据库中持久化了的对象。下面的例子中，我们创建一个`OrderProcessor`，它接受`order`和`CreditCard`的实例。然后调用`Braintree`的API用来返回一个布尔值，判断是否真正发生了付款。

```ruby
class OrderProcessor
  def initialize(order, credit_card)
    @order = order
    @credit_card = credit_card
  end

  def process
    charge_successful? charge_customer
  end

  private

  def charge_successful?(result)
    # processes the result to determine if the result is valid,
    # operating on the order and credit_card instance variables to
    # add errors, send emails, or track Braintree's transaction id
  end

  def charge_customer
    # runs Braintree::Customer.sale() with the appropriate options
  end
end
```

没有数据访问了数据库，所以我们可以这样写测试

```ruby
describe OrderProcessor do
  let(:transaction_id) { '1234' }
  let(:order) { build_stubbed(:order) }
  let(:credit_card) { build_stubbed(:credit_card) }

  subject { OrderProcessor.new(order, credit_card) }

  context 'when the Braintree result is valid' do
    before do
      MockBraintree.stub_successful_customer_sale(transaction_id: transaction_id)
    end

    it 'assigns the transaction id to the order' do
      subject.process
      order.transaction_id.should == transaction_id
    end

    it 'returns true for #process' do
      subject.process.should be
    end

    it 'does not assign any errors to the credit card' do
      subject.process
      credit_card.errors.should be_empty
    end
  end

  context 'when the Braintree result is invalid' do
    before do
      MockBraintree.stub_unsuccessful_customer_sale
    end

    it 'does not assign the transaction id to the order' do
      subject.process
      order.transaction_id.should be_nil
    end

    it 'returns false for #process' do
      subject.process.should_not be
    end

    it 'assigns errors to the credit card' do
      subject.process
      credit_card.errors.should_not be_empty
    end
  end
end
```


[Factory Girl Callbacks](http://robots.thoughtbot.com/aint-no-calla-back-girl)

[Use Factory Girl's build_stubbed for a Faster Test Suite](http://robots.thoughtbot.com/use-factory-girls-build-stubbed-for-a-faster-test)