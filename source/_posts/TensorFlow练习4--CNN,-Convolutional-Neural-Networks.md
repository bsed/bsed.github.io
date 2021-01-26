---
title: "TensorFlow练习4: CNN, Convolutional Neural Networks"
categories: [ "大数据/NLP" ]
tags: [ "python","tensorflow","斗大的熊猫" ]
draft: false
slug: "tensorflow-exercise-4-cnn-convolutional-neural-networks"
date: "2019-09-10 09:21:00"
---

Convolutional Neural Networks翻译为卷积神经网络，常用在图像识别和语音分析等领域。CNN详细介绍参看：

- https://en.wikipedia.org/wiki/Convolutional_neural_network
- http://blog.csdn.net/zouxy09/article/details/8781543
- http://deeplearning.net/tutorial/lenet.html

#### 使用TensorFlow创建CNN


<!--more-->


```python
import tensorflow as tf

import numpy as np

 

# 下载mnist数据集

from tensorflow.examples.tutorials.mnist import input_data

mnist = input_data.read_data_sets('/tmp/', one_hot=True)

 

 

n_output_layer = 10

 

# 定义待训练的神经网络

def convolutional_neural_network(data):

	weights = {'w_conv1':tf.Variable(tf.random_normal([5,5,1,32])),

              'w_conv2':tf.Variable(tf.random_normal([5,5,32,64])),

              'w_fc':tf.Variable(tf.random_normal([7*7*64,1024])),

              'out':tf.Variable(tf.random_normal([1024,n_output_layer]))}

 

	biases = {'b_conv1':tf.Variable(tf.random_normal([32])),

              'b_conv2':tf.Variable(tf.random_normal([64])),

              'b_fc':tf.Variable(tf.random_normal([1024])),

              'out':tf.Variable(tf.random_normal([n_output_layer]))}

 

	data = tf.reshape(data, [-1,28,28,1])

 

	conv1 = tf.nn.relu(tf.add(tf.nn.conv2d(data, weights['w_conv1'], strides=[1,1,1,1], padding='SAME'), biases['b_conv1']))

	conv1 = tf.nn.max_pool(conv1, ksize=[1,2,2,1], strides=[1,2,2,1], padding='SAME')

 

	conv2 = tf.nn.relu(tf.add(tf.nn.conv2d(conv1, weights['w_conv2'], strides=[1,1,1,1], padding='SAME'), biases['b_conv2']))

	conv2 = tf.nn.max_pool(conv2, ksize=[1,2,2,1], strides=[1,2,2,1], padding='SAME')

 

	fc = tf.reshape(conv2, [-1,7*7*64])

	fc = tf.nn.relu(tf.add(tf.matmul(fc, weights['w_fc']), biases['b_fc']))

 

	# dropout剔除一些"神经元"

	#fc = tf.nn.dropout(fc, 0.8)

 

	output = tf.add(tf.matmul(fc, weights['out']), biases['out'])

	return output

 

 

# 每次使用100条数据进行训练

batch_size = 100

 

X = tf.placeholder('float', [None, 28*28]) 

Y = tf.placeholder('float')

# 使用数据训练神经网络

def train_neural_network(X, Y):

	predict = convolutional_neural_network(X)

	cost_func = tf.reduce_mean(tf.nn.softmax_cross_entropy_with_logits(predict, Y))

	optimizer = tf.train.AdamOptimizer().minimize(cost_func)  # learning rate 默认 0.001 

 

	epochs = 1

	with tf.Session() as session:

		session.run(tf.initialize_all_variables())

		epoch_loss = 0

		for epoch in range(epochs):

			for i in range( int(mnist.train.num_examples/batch_size) ):

				x, y = mnist.train.next_batch(batch_size)

				_, c = session.run([optimizer, cost_func], feed_dict={X:x,Y:y})

				epoch_loss += c

			print(epoch, ' : ', epoch_loss)

 

		correct = tf.equal(tf.argmax(predict,1), tf.argmax(Y,1))

		accuracy = tf.reduce_mean(tf.cast(correct,'float'))

		print('准确率: ', accuracy.eval({X:mnist.test.images, Y:mnist.test.labels}))

 

train_neural_network(X,Y)
```

执行结果：

```
准确率:  0.9789
```



------

下面使用tflearn重写上面代码，tflearn是TensorFlow的高级封装，类似[Keras](http://blog.topspeedsnail.com/archives/10427)。

tflearn提供了更简单、直观的接口。和scikit-learn差不多，代码如下：

```python

import tflearn

from tflearn.layers.conv import conv_2d, max_pool_2d

from tflearn.layers.core import input_data, dropout, fully_connected

from tflearn.layers.estimator import regression

 

train_x, train_y, test_x, test_y = tflearn.datasets.mnist.load_data(one_hot=True)

 

train_x = train_x.reshape(-1,28,28,1)

test_x = test_x.reshape(-1,28,28,1)

 

# 定义神经网络模型

conv_net = input_data(shape=[None,28,28,1], name='input')

conv_net = conv_2d(conv_net, 32, 2, activation='relu')

conv_net = max_pool_2d(conv_net ,2)

conv_net = conv_2d(conv_net, 64, 2, activation='relu')

conv_net = max_pool_2d(conv_net ,2)

conv_net = fully_connected(conv_net, 1024, activation='relu')

conv_net = dropout(conv_net, 0.8)

conv_net = fully_connected(conv_net, 10, activation='softmax')

conv_net = regression(conv_net, optimizer='adam', loss='categorical_crossentropy', name='output')

 

model = tflearn.DNN(conv_net)

 

# 训练

model.fit({'input':train_x}, {'output':train_y}, n_epoch=13, 

          validation_set=({'input':test_x}, {'output':test_y}), 

          snapshot_step=300, show_metric=True, run_id='mnist')

 

model.save('mnist.model')   # 保存模型

 

"""

model.load('mnist.model')   # 加载模型

model.predict([test_x[1]])  # 预测

"""
```

![屏幕快照-2016-11-17-上午10.22.57.png][1]
![屏幕快照-2016-11-17-上午10.55.37.png][2]

> **本文是对斗大的熊猫 tensorflow 系列 文章的备份。D啪乐嗯QQ: 193093683 **

  [1]: https://imgs.gnux.cn/usr/uploads/2019/09/545943224.png
  [2]: https://imgs.gnux.cn/usr/uploads/2019/09/4132857921.png