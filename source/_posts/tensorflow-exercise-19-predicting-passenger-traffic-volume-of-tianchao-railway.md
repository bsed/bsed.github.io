---
title: TensorFlow练习19: 预测天朝铁路客运量
date: 2019-09-25 17:06:00
updated: 2019-09-08 17:16:52
tags: 
- python
- tensorflow
- 斗大的熊猫
categories: 
- bigdata

---
以前做的练习还没有涉及过时间序列数据（洋文Time Series Data），一个最明显的例子是股票价格。

> 时间序列数据是指在不同时间点上收集到的数据，这类数据反映了某一事物、现象等随时间的变化状态或程度。

一年一度的春节又要来了，火车票已经开售。本帖就简单预测一下未来几个月天朝铁路客运量。

## 铁路客运量历史数据

[铁路客运量.csv](http://blog.topspeedsnail.com/wp-content/uploads/2016/12/铁路客运量.csv)（2005-2016月度数据），数据来源于国家捅计菊。

使用matplotlib画出数据走势：


<!--more-->


```python
import matplotlib.pyplot as plt

import pandas as pd

import requests

import io

import numpy as np

 

url = 'http://blog.topspeedsnail.com/wp-content/uploads/2016/12/铁路客运量.csv'

ass_data = requests.get(url).content

 

df = pd.read_csv(io.StringIO(ass_data.decode('utf-8')))  # python2使用StringIO.StringIO

 

data = np.array(df['铁路客运量_当期值(万人)'])

# normalize

normalized_data = (data - np.mean(data)) / np.std(data)

 

plt.figure()

plt.plot(data)

plt.show()
```

![TensorFlow练习19: 预测天朝铁路客运量](TensorFlow练习19%20预测天朝铁路客运量%20–%20WTF%20Daily%20Blog_files/屏幕快照-2016-12-18-下午1.17.44.png)总体呈增长趋势, 每年又有淡季和旺季



代码：

```python
import numpy as np

import tensorflow as tf

import matplotlib.pyplot as plt

import pandas as pd

import requests

import io

 

# 加载数据

url = 'http://blog.topspeedsnail.com/wp-content/uploads/2016/12/铁路客运量.csv'

ass_data = requests.get(url).content

 

df = pd.read_csv(io.StringIO(ass_data.decode('utf-8')))  # python2使用StringIO.StringIO

 

data = np.array(df['铁路客运量_当期值(万人)'])

# normalize

normalized_data = (data - np.mean(data)) / np.std(data)

 

seq_size = 3

train_x, train_y = [], []

for i in range(len(normalized_data) - seq_size - 1):

	train_x.append(np.expand_dims(normalized_data[i : i + seq_size], axis=1).tolist())

	train_y.append(normalized_data[i + 1 : i + seq_size + 1].tolist())

 

input_dim = 1

X = tf.placeholder(tf.float32, [None, seq_size, input_dim])

Y = tf.placeholder(tf.float32, [None, seq_size])

 

# regression

def ass_rnn(hidden_layer_size=6):

	W = tf.Variable(tf.random_normal([hidden_layer_size, 1]), name='W')

	b = tf.Variable(tf.random_normal([1]), name='b')

	cell = tf.nn.rnn_cell.BasicLSTMCell(hidden_layer_size)

	outputs, states = tf.nn.dynamic_rnn(cell, X, dtype=tf.float32)

	W_repeated = tf.tile(tf.expand_dims(W, 0), [tf.shape(X)[0], 1, 1])

	out = tf.batch_matmul(outputs, W_repeated) + b

	out = tf.squeeze(out)

	return out

 

def train_rnn():

	out = ass_rnn()

 

	loss = tf.reduce_mean(tf.square(out - Y))

	train_op = tf.train.AdamOptimizer(learning_rate=0.003).minimize(loss)

 

	saver = tf.train.Saver(tf.global_variables())

	with tf.Session() as sess:

		#tf.get_variable_scope().reuse_variables()

		sess.run(tf.global_variables_initializer())

 

		for step in range(10000):

			_, loss_ = sess.run([train_op, loss], feed_dict={X: train_x, Y: train_y})

			if step % 10 == 0:

				# 用测试数据评估loss

				print(step, loss_)

		print("保存模型: ", saver.save(sess, 'ass.model'))

 

#train_rnn()

 

def prediction():

	out = ass_rnn()

 

	saver = tf.train.Saver(tf.global_variables())

	with tf.Session() as sess:

		#tf.get_variable_scope().reuse_variables()

		saver.restore(sess, './ass.model')

		

		prev_seq = train_x[-1]

		predict = []

		for i in range(12):

			next_seq = sess.run(out, feed_dict={X: [prev_seq]})

			predict.append(next_seq[-1])

			prev_seq = np.vstack((prev_seq[1:], next_seq[-1]))

 

		plt.figure()

		plt.plot(list(range(len(normalized_data))), normalized_data, color='b')

		plt.plot(list(range(len(normalized_data), len(normalized_data) + len(predict))), predict, color='r')

		plt.show()

 

#prediction()
```


![TensorFlow练习19: 预测天朝铁路客运量](TensorFlow练习19%20预测天朝铁路客运量%20–%20WTF%20Daily%20Blog_files/屏幕快照-2016-12-18-下午5.03.47.png)红色曲线是预测值



> 网友提问：怎么判断模型已训练到最优？
>
> 简单回答：使用TensorBoard绘出loss和准确率。

## 问题：
《TensorFlow练习19: 预测天朝铁路客运量》有11个想法
hsc说道：
2017年6月10日 下午3:56
我印出接近的圖形了，但是要學習到 200000 次
我把其中一段 code 改成直接讀本地端的檔案
貼上來分享給大家

#url = ‘http://blog.topspeedsnail.com/wp-content/uploads/2016/12/铁路客运量.csv’
ass_data = requests.get(url).content
#df = pd.read_csv(io.StringIO(ass_data.decode(‘utf-8’)))

df = pd.read_csv(‘鐵路客運量.csv’)

回复
hsc说道：
2017年6月10日 下午3:58
有一行程式少 #
#url = ‘http://blog.topspeedsnail.com/wp-content/uploads/2016/12/铁路客运量.csv’
#ass_data = requests.get(url).content
#df = pd.read_csv(io.StringIO(ass_data.decode(‘utf-8’)))

df = pd.read_csv(‘鐵路客運量.csv’)

回复
SKy说道：
2017年4月15日 下午9:50
最后预测和博主的差距有点大，TensorFlow版本是12.0

回复
梦溪说道：
2017年2月28日 下午3:40
您好，我运行时报了如下错误：在ass_rnn(hidden_layer_size=6):中的
W_repeated = tf.tile(tf.expand_dims(W, 0), [tf.shape(X)[0], 1, 1])
这一行，能帮忙看下是什么问题吗~万分感谢！
File “/Users/IJCAI/traffic.py”, line 38, in ass_rnn
W_repeated = tf.tile(tf.expand_dims(W, 0), [tf.shape(X)[0], 1, 1])
File “/Library/Python/2.7/site-packages/tensorflow/python/ops/gen_array_ops.py”, line 1521, in tile
name=name)
File “/Library/Python/2.7/site-packages/tensorflow/python/ops/op_def_library.py”, line 411, in apply_op
as_ref=input_arg.is_ref)
File “/Library/Python/2.7/site-packages/tensorflow/python/framework/ops.py”, line 566, in convert_to_tensor
ret = conversion_func(value, dtype=dtype, name=name, as_ref=as_ref)
File “/Library/Python/2.7/site-packages/tensorflow/python/ops/constant_op.py”, line 179, in _constant_tensor_conversion_function
return constant(v, dtype=dtype, name=name)
File “/Library/Python/2.7/site-packages/tensorflow/python/ops/constant_op.py”, line 162, in constant
tensor_util.make_tensor_proto(value, dtype=dtype, shape=shape))
File “/Library/Python/2.7/site-packages/tensorflow/python/framework/tensor_util.py”, line 332, in make_tensor_proto
_AssertCompatible(values, dtype)
File “/Library/Python/2.7/site-packages/tensorflow/python/framework/tensor_util.py”, line 272, in _AssertCompatible
(dtype.name, repr(mismatch), type(mismatch).__name__))
TypeError: Expected int32, got list containing Tensors of type ‘_Message’ instead.

回复
hzy_zg说道：
2017年1月11日 下午10:38
train()和prediction()放在一个文件中同时run会出错呢？

回复
Geoffrey Hung说道：
2017年2月8日 下午6:27
一樣…成功train了model但最後prediction()有error, 有沒有人完整run到最後prediction的圖？謝～

ValueError: Variable lstm/RNN/BasicLSTMCell/Linear/Matrix already exists, disallowed. Did you mean to set reuse=True in VarScope? Originally defined at:

回复
event说道：
2017年2月17日 下午8:26
对代码先恢复train_rnn()并且注释掉prediction()，即先运行定义的train_rnn函数，得到ass.model模型；再对代码注释掉train_rnn()并且恢复prediction()，即不再运行定义的train_rnn函数，直接运行定义的prediction函数。 ps：得到的图形和博主的图差好多，还不知道哪里的问题

回复
梦溪说道：
2017年2月28日 下午3:54
请问用的tensorflow的版本是多少？

回复
蜗牛说道：
2017年6月10日 上午9:14
从 tf.global_variables() 上看 ， 应该是tf 0.12的； 或者是master 版本的； 我用的是tf 0.10 需要把 tf.global_variables() 改成 tf.all_variables() ； 把 tf.global_variables_initializer() 改成 tf.initialize_all_variables() ， 按照楼上的event 的方式运行应该没有问题 ：）

SKy说道：
2017年4月15日 下午8:50
不知道是不是博主的数据有问题，确实跑出来的数据和博主的差太多了

回复
long说道：
2017年1月10日 下午8:07
如果有多趟火车呢？能不能同时预测？即同时预测n趟火车客运量？能否做个练习？

> **本文是对斗大的熊猫tensorflow系列文章的备份。D啪乐嗯QQ: 193093683 **