---
title: TensorFlow练习12: 判断男声女声
date: 2019-09-18 16:17:00
updated: 2019-09-08 16:19:25
tags: 
- python
- tensorflow
- 斗大的熊猫
categories: 
- bigdata

---
本帖训练一个简单的神经网络模型，用来判断声音是男是女。

本帖数据集取自[voice-gender](https://github.com/primaryobjects/voice-gender)项目，这个项目使用了n种分类模型，并比较了准确率，但是它没有使用神经网络模型，本帖算是一个补充。

## 数据集

这个[数据集](http://blog.topspeedsnail.com/wp-content/uploads/2016/12/voice.csv)是经过R语言处理过的，它提取出了.WAV文件的一些声音属性。如果你想自己从wav文件中提取声音属性，参看voice-gender项目中一个叫sound.R源码文件。

数据集字段：”meanfreq”,”sd”,”median”,”Q25″,”Q75″,”IQR”,”skew”,”kurt”,”sp.ent”,”sfm”,”mode”,”centroid”,”meanfun”,”minfun”,”maxfun”,”meandom”,”mindom”,”maxdom”,”dfrange”,”modindx”,”label”。最后一个字段标记了是男声还是女声，前面字段是声音属性。


<!--more-->


## 代码


```python
import os

import requests

import pandas as pd

import numpy as np

import random

import tensorflow as tf  # 0.12

from sklearn.model_selection import train_test_split

 

# 下载数据集

if not os.path.exists('voice.csv'):

	url = 'http://blog.topspeedsnail.com/wp-content/uploads/2016/12/voice.csv'

	data = requests.get(url).content

	with open('voice.csv', 'wb') as f:

		f.write(data)

 

voice_data = pd.read_csv('voice.csv')

#print(voice_data.head())

#print(voice_data.tail())

 

voice_data = voice_data.values

# 分离声音特性和分类

voices = voice_data[:, :-1]

labels = voice_data[:, -1:]  #  ['male']  ['female']

 

# 把分类转为one-hot

labels_tmp = []

for label in labels:

	tmp = []

	if label[0] == 'male':

		tmp = [1.0, 0.0]

	else:  # 'female'

		tmp = [0.0, 1.0]

	labels_tmp.append(tmp)

labels = np.array(labels_tmp)

 

# shuffle

voices_tmp = []

lables_tmp = []

index_shuf = [i for i in range(len(voices))]

random.shuffle(index_shuf)

for i in index_shuf:

    voices_tmp.append(voices[i])

    lables_tmp.append(labels[i])

voices = np.array(voices_tmp)

labels = np.array(lables_tmp)

 

train_x, test_x, train_y, test_y = train_test_split(voices, labels, test_size=0.1)

 

banch_size = 64

n_banch = len(train_x) // banch_size

 

X = tf.placeholder(dtype=tf.float32, shape=[None, voices.shape[-1]])  # 20

Y = tf.placeholder(dtype=tf.float32, shape=[None, 2])

 

# 3层（feed-forward）

def neural_network():

	w1 = tf.Variable(tf.random_normal([voices.shape[-1], 512], stddev=0.5))

	b1 = tf.Variable(tf.random_normal([512]))

	output = tf.matmul(X, w1) + b1

	

	w2 = tf.Variable(tf.random_normal([512, 1024],stddev=.5))

	b2 = tf.Variable(tf.random_normal([1024]))

	output = tf.nn.softmax(tf.matmul(output, w2) + b2)

 

	w3 = tf.Variable(tf.random_normal([1024, 2],stddev=.5))

	b3 = tf.Variable(tf.random_normal([2]))

	output = tf.nn.softmax(tf.matmul(output, w3) + b3)

	return output

 

# 训练神经网络

def train_neural_network():

	output = neural_network()

 

	cost = tf.reduce_mean(tf.reduce_sum(tf.nn.softmax_cross_entropy_with_logits(output, Y)))

	lr = tf.Variable(0.001, dtype=tf.float32, trainable=False)

	opt = tf.train.AdamOptimizer(learning_rate=lr)

	var_list = [t for t in tf.trainable_variables()]

	train_step = opt.minimize(cost, var_list=var_list)

 

	#saver = tf.train.Saver(tf.global_variables())

	#saver.restore(sess, tf.train.latest_checkpoint('.'))

	with tf.Session() as sess:

		sess.run(tf.global_variables_initializer())

		#summary_writer = tf.train.SummaryWriter('voices')

		for epoch in range(200):

			sess.run(tf.assign(lr, 0.001 * (0.97 ** epoch)))

 

			for banch in range(n_banch):

				voice_banch = train_x[banch*banch_size:(banch+1)*(banch_size)]

				label_banch = train_y[banch*banch_size:(banch+1)*(banch_size)]

				_, loss = sess.run([train_step, cost], feed_dict={X: voice_banch, Y: label_banch})

				print(epoch, banch, loss)

 

		# 准确率

		prediction = tf.equal(tf.argmax(output, 1), tf.argmax(Y, 1))

		accuracy = tf.reduce_mean(tf.cast(prediction, dtype=tf.float32))

		accuracy = sess.run(accuracy, feed_dict={X: test_x, Y: test_y})

		print("准确率", accuracy)

 

		#prediction = sess.run(output, feed_dict={X: test_x})

 

train_neural_network()
```



Share the post "TensorFlow练习12: 判断男声女声"

- [Facebook](https://www.facebook.com/sharer/sharer.php?u=http://blog.topspeedsnail.com/archives/10729)
-  

- [Google+](https://plus.google.com/share?url=http://blog.topspeedsnail.com/archives/10729)
-  

- [Twitter](https://twitter.com/intent/tweet?source=webclient&original_referer=http://blog.topspeedsnail.com/archives/10729&text=TensorFlow练习12%3A 判断男声女声&url=http://blog.topspeedsnail.com/archives/10729)
-  

- [Weibo](http://service.weibo.com/share/share.php?title=TensorFlow练习12%3A 判断男声女声+%3A+本帖训练一个简单的神经网络模型，用来判断声音是男是女。  本帖数据集取自voice-gender项目，这个项目使用了n种分类模型，并比较了准确率，但是它没有使用神经网络模型，本&url=http://blog.topspeedsnail.com/archives/10729)
-  


> **本文是对斗大的熊猫tensorflow系列文章的备份。D啪乐嗯QQ: 193093683 **