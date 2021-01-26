---
title: "TensorFlow练习9: 生成妹子图（PixelCNN）"
categories: [ "大数据/NLP" ]
tags: [ "python","tensorflow","斗大的熊猫" ]
draft: false
slug: "tensorflow-exercise-9-generating-pixel-cnn"
date: "2019-09-16 16:02:00"
---

前一帖[生成音乐](http://blog.topspeedsnail.com/archives/10508)，本帖生成图片。本文使用TensorFlow实现论文《[Conditional Image Generation with PixelCNN Decoders](https://arxiv.org/abs/1606.05328)》，它是基于PixelCNN架构的模型，最早出现在《[Pixel Recurrent Neural Networks](https://arxiv.org/abs/1601.06759)》一文。

## 使用的图片数据

我本想使用ImageNet做为图片来源，就像论文中使用的。ImageNet图像有现成的分类，抓取也容易，但是由于很多源都被防火墙屏蔽，下载速度堪忧。《[OpenCV之使用Haar Cascade进行对象识别](http://blog.topspeedsnail.com/archives/10511)》

我看到网上有很多爬妹纸图的Python脚本，额，我爬了几天几夜的妹纸图（特别暴露那种），额，我就想看看PixelCNN最后能生成什么鬼。


<!--more-->


如果你懒的爬图片，可以使用我抓取的图片（分成两部分）：

- https://pan.baidu.com/s/1kVSA8z9 (密码: atqm)
- https://pan.baidu.com/s/1ctbd9O (密码: kubu)

## 数据预处理

下载的图片分布在多个目录，把图片汇总到一个新目录：


```python
import os

 

old_dir = 'images'

new_dir = 'girls'

if not os.path.exists(new_dir):

    os.makedirs(new_dir)

 

count = 0

for (dirpath, dirnames, filenames) in os.walk(old_dir):

    for filename in filenames:

        if filename.endswith('.jpg'):

            new_filename = str(count) + '.jpg'

            os.rename(os.sep.join([dirpath, filename]), os.sep.join([new_dir, new_filename]))

            print(os.sep.join([dirpath, filename]))

            count += 1

print("Total Picture: ", count)
```
使用《[open_nsfw: 基于Caffe的成人图片识别模型](http://blog.topspeedsnail.com/archives/9440)》剔除掉和妹子图不相关的图片，给open_nsfw输入要检测的图片，它会返回图片评级（0-1），等级越高，图片越黄越暴力。使用OpenCV应该也不难。

为了减小计算量，我把图像缩放为64×64像素：


```bash
import os

import cv2

import numpy as np

 

image_dir = 'girls'

new_girl_dir = 'little_girls'

if not os.path.exists(new_girl_dir):

    os.makedirs(new_girl_dir)

 

for img_file in os.listdir(image_dir):

    img_file_path = os.path.join(image_dir, img_file)

    img = cv2.imread(img_file_path)

    if img is None:

        print("image read fail")

        continue

    height, weight, channel = img.shape

    if height < 200 or weight < 200 or channel != 3: 

        continue

    # 你也可以转为灰度图片(channel=1)，加快训练速度

    # 把图片缩放为64x64

    img = cv2.resize(img, (64, 64))

    new_file = os.path.join(new_girl_dir, img_file)

    cv2.imwrite(new_file, img)

    print(new_file)
```

去除重复图片：



```python
import os

import cv2

import numpy as np

 

# 判断两张图片是否完全一样（使用哈希应该要快很多）

def is_same_image(img_file1, img_file2):

    img1 = cv2.imread(img_file1)

    img2 = cv2.imread(img_file2)

    if img1 is None or img2 is None:

        return False

    if img1.shape == img2.shape and not (np.bitwise_xor(img1, img2).any()):

        return True

    else:

        return False

 

# 去除重复图片

file_list = os.listdir('little_girls')

try:

	for img1 in file_list:

		print(len(file_list))

		for img2 in file_list:

			if img1 != img2:

				if is_same_image('little_girls/'+img1, 'little_girls/'+img2) is True:

					print(img1, img2)

					os.remove('little_girls/'+img1)

		file_list.remove(img1)

except Exception as e:

	print(e)
```



## PixelCNN生成妹纸图完整代码

下面代码只实现了unconditional模型（无条件），没有实现conditional和autoencoder模型。详细信息，请参看论文。

```python
# -*- coding: utf-8 -*-

 

import tensorflow as tf

import numpy as np

import os

import cv2

 

# 如果使用mnist数据集，把MNIST设置为True

MNIST = False

 

if MNIST == True:

	from tensorflow.examples.tutorials.mnist import input_data

	data = input_data.read_data_sets('/tmp/')

	image_height = 28

	image_width = 28

	image_channel = 1

 

	batch_size = 128

	n_batches = data.train.num_examples // batch_size

else:

	picture_dir = 'little_girls'

	picture_list = []

	# 建议不要把图片一次加载到内存，为了节省内存，最好边加载边使用

	for (dirpath, dirnames, filenames) in os.walk(picture_dir):

		for filename in filenames:

			if filename.endswith('.jpg'):

				picture_list.append(os.sep.join([dirpath, filename]))

 

	print("图像总数: ", len(picture_list))

 

	# 图像大小和Channel

	image_height = 64

	image_width = 64

	image_channel = 3

 

	# 每次使用多少样本训练

	batch_size = 128

	n_batches = len(picture_list) // batch_size

 

	#图片格式对应输入X

	img_data = []

	for img_file in picture_list:

		img_data.append(cv2.imread(img_file))

	img_data = np.array(img_data)

	img_data = img_data / 255.0

	#print(img_data.shape)   # (44112, 64, 64, 3)

 

 

X = tf.placeholder(tf.float32, shape=[None, image_height, image_width, image_channel])

 

def gated_cnn(W_shape_, fan_in, gated=True, payload=None, mask=None, activation=True):

	W_shape = [W_shape_[0], W_shape_[1], fan_in.get_shape()[-1], W_shape_[2]]

	b_shape = W_shape_[2]

 

	def get_weights(shape, name, mask=None):

		weights_initializer = tf.contrib.layers.xavier_initializer()

		W = tf.get_variable(name, shape, tf.float32, weights_initializer)

 

		if mask:

			filter_mid_x = shape[0]//2

			filter_mid_y = shape[1]//2

			mask_filter = np.ones(shape, dtype=np.float32)

			mask_filter[filter_mid_x, filter_mid_y+1:, :, :] = 0.

			mask_filter[filter_mid_x+1:, :, :, :] = 0.

 

			if mask == 'a':

				mask_filter[filter_mid_x, filter_mid_y, :, :] = 0.

 

			W *= mask_filter 

		return W

 

	if gated:

		W_f = get_weights(W_shape, "v_W", mask=mask)

		W_g = get_weights(W_shape, "h_W", mask=mask)

 

		b_f = tf.get_variable("v_b", b_shape, tf.float32, tf.zeros_initializer)

		b_g = tf.get_variable("h_b", b_shape, tf.float32, tf.zeros_initializer)

 

		conv_f = tf.nn.conv2d(fan_in, W_f, strides=[1,1,1,1], padding='SAME')

		conv_g = tf.nn.conv2d(fan_in, W_g, strides=[1,1,1,1], padding='SAME')

		if payload is not None:

			conv_f += payload

			conv_g += payload

 

		fan_out = tf.mul(tf.tanh(conv_f + b_f), tf.sigmoid(conv_g + b_g))

	else:

		W = get_weights(W_shape, "W", mask=mask)

		b = tf.get_variable("b", b_shape, tf.float32, tf.zeros_initializer)

		conv = tf.nn.conv2d(fan_in, W, strides=[1,1,1,1], padding='SAME')

		if activation: 

			fan_out = tf.nn.relu(tf.add(conv, b))

		else:

			fan_out = tf.add(conv, b)

 

	return fan_out

 

def pixel_cnn(layers=12, f_map=32):

	v_stack_in, h_stack_in = X, X

 

	for i in range(layers):

		filter_size = 3 if i > 0 else 7

		mask = 'b' if i > 0 else 'a'

		residual = True if i > 0 else False

		i = str(i)

 

		with tf.variable_scope("v_stack"+i):

			v_stack = gated_cnn([filter_size, filter_size, f_map], v_stack_in, mask=mask)

			v_stack_in = v_stack

 

		with tf.variable_scope("v_stack_1"+i):

			v_stack_1 = gated_cnn([1, 1, f_map], v_stack_in, gated=False, mask=mask)

 

		with tf.variable_scope("h_stack"+i):

			h_stack = gated_cnn([1, filter_size, f_map], h_stack_in, payload=v_stack_1, mask=mask)

 

		with tf.variable_scope("h_stack_1"+i):

			h_stack_1 = gated_cnn([1, 1, f_map], h_stack, gated=False, mask=mask)

			if residual:

				h_stack_1 += h_stack_in

			h_stack_in = h_stack_1

 

	with tf.variable_scope("fc_1"):

		fc1 = gated_cnn([1, 1, f_map], h_stack_in, gated=False, mask='b')

 

	color = 256

	with tf.variable_scope("fc_2"):

		fc2 = gated_cnn([1, 1, image_channel * color], fc1, gated=False, mask='b', activation=False)

		fc2 = tf.reshape(fc2, (-1, color))

 

		return fc2

 

def train_pixel_cnn():

	output = pixel_cnn()

 

	loss = tf.reduce_mean(tf.nn.sparse_softmax_cross_entropy_with_logits(output, tf.cast(tf.reshape(X, [-1]), dtype=tf.int32)))

	trainer = tf.train.RMSPropOptimizer(1e-3)

	gradients = trainer.compute_gradients(loss)

	clipped_gradients = [(tf.clip_by_value(_[0], -1, 1), _[1]) for _ in gradients]

	optimizer = trainer.apply_gradients(clipped_gradients)

 

	with tf.Session() as sess:

		sess.run(tf.initialize_all_variables())

 

		saver = tf.train.Saver(tf.trainable_variables())

 

		for epoch in range(50):

			for batch in range(n_batches):

 

				if MNIST == True:

					batch_X, _ = data.train.next_batch(batch_size)

					batch_X = batch_X.reshape([batch_size, image_height, image_width, image_channel])

				else:

					batch_X = img_data[batch_size * batch : batch_size * (batch + 1)]

 

				_, cost = sess.run([optimizer, loss], feed_dict={X:batch_X})

				print("epoch:", epoch, '  batch:', batch,'  cost:', cost)

			if epoch % 7 == 0:

				saver.save(sess, "girl.ckpt", global_step=epoch)

 

# 训练

train_pixel_cnn()

 

def generate_girl():

	output = pixel_cnn()

 

	predict = tf.reshape(tf.multinomial(tf.nn.softmax(output), num_samples=1, seed=100), tf.shape(X))

	#predict_argmax = tf.reshape(tf.argmax(tf.nn.softmax(output), dimension=tf.rank(output) - 1), tf.shape(X))

 

	with tf.Session() as sess: 

		sess.run(tf.initialize_all_variables())

 

		saver = tf.train.Saver(tf.trainable_variables())

		saver.restore(sess, 'girl.ckpt-49')

 

		pics = np.zeros((1*1, image_height, image_width, image_channel), dtype=np.float32)

 

		for i in range(image_height):

			for j in range(image_width):

				for k in range(image_channel):

					next_pic = sess.run(predict, feed_dict={X:pics})

					pics[:, i, j, k] = next_pic[:, i, j, k]

 

		cv2.imwrite('girl.jpg', pics[0])

		print('生成妹子图: girl.jpg')

 

# 生成图像

generate_girl()
```

额，妹子图正在训练中…

补充练习：使用[OpenCV提取图像中的脸](http://blog.topspeedsnail.com/archives/10511)，然后使用上面模型进行训练，看看能生成什么。

- [Deep Generative Image Models using a Laplacian Pyramid of Adversarial Networks](https://arxiv.org/pdf/1506.05751v1.pdf)
- https://github.com/awentzonline/image-analogies
- https://github.com/ericjang/draw



《TensorFlow练习9: 生成妹子图（PixelCNN）》有16个想法
hitdagang说道：
2017年7月8日 下午2:54
我是学生，在尝试这段代码的时候存在：
builtins.TypeError: __init__() got an unexpected keyword argument ‘partition_info’
不清楚发生的原因是什么？
我是用的环境是Anaconda+Python3.5+Tensorflow

回复
dd说道：
2017年5月5日 下午9:09
博主，你在哪里爬的图片？我发现很多图片都很像，看不清脸，想问问你这些脸大的图片哪里找的。谢谢，我想做人脸生成用。

回复
dd说道：
2017年5月5日 下午9:10
很小，不是很像……

回复
dk说道：
2017年3月19日 下午11:42
跑到第76行时，报错，错误如下，求大神们指教，谢谢
b_f = tf.get_variable(“v_b”, b_shape, tf.float32, tf.zeros_initializer)

File “/Users/dk/tensorflow/lib/python2.7/site-packages/tensorflow/python/ops/variables.py”, line 197, in __init__
expected_shape=expected_shape)
File “/Users/dk/tensorflow/lib/python2.7/site-packages/tensorflow/python/ops/variables.py”, line 274, in _init_from_args
initial_value(), name=”initial_value”, dtype=dtype)
File “/Users/dk/tensorflow/lib/python2.7/site-packages/tensorflow/python/ops/variable_scope.py”, line 673, in
shape.as_list(), dtype=dtype, partition_info=partition_info)
TypeError: __init__() got multiple values for keyword argument ‘dtype’

回复
Andy说道：
2017年3月16日 下午3:33
好想知道生成的妹子图是啥样的

回复
Kk说道：
2017年3月14日 下午8:09
我理解不鸟为啥这里要有两个for循环, 不是一个’for batch in range(n_batches)’ 就可以了么?

for epoch in range(50):
for batch in range(n_batches):

回复
JoneNash说道：
2016年12月26日 下午9:48
博主，最终生成的妹子图片是什么样子的？我自己调试并运行了一下，并没有取得很好的效果，图片就像彩色雪花，看不出特别之处。和博主不同的是，我在做训练集时采用了较少的训练集，不清楚是训练集太少还是本身原理性问题，引起效果不好？

感谢~

回复
Fancy说道：
2016年12月8日 下午8:06
你好，非常感谢你能分享你的实验，从中受益良多。
我下载了你的数据集，也运行了你的代码，但是出现了一点点小问题，我解决不了，不知道你时候能有时间看看这个错误为什么会出现。
多谢！

ValueError: Variable v_stack0/v_W already exists, disallowed. Did you mean to set reuse=True in VarScope? Originally defined at:

File “generate.py”, line 57, in get_weights
W = tf.get_variable(name, shape, tf.float32, weights_initializer)
File “generate.py”, line 73, in gated_cnn
W_f = get_weights(W_shape, “v_W”, mask=mask)
File “generate.py”, line 107, in pixel_cnn
v_stack = gated_cnn([filter_size, filter_size, f_map], v_stack_in, mask=mask)

我是初学者，运行时会出现以下结果
Traceback (most recent call last):
File “generate.py”, line 187, in
generate_girl()
File “generate.py”, line 164, in generate_girl
output = pixel_cnn()
File “generate.py”, line 107, in pixel_cnn
v_stack = gated_cnn([filter_size, filter_size, f_map], v_stack_in, mask=mask)
File “generate.py”, line 73, in gated_cnn
W_f = get_weights(W_shape, “v_W”, mask=mask)
File “generate.py”, line 57, in get_weights
W = tf.get_variable(name, shape, tf.float32, weights_initializer)
File “/usr/local/lib/python2.7/dist-packages/tensorflow/python/ops/variable_scope.py”, line 1022, in get_variable
custom_getter=custom_getter)
File “/usr/local/lib/python2.7/dist-packages/tensorflow/python/ops/variable_scope.py”, line 849, in get_variable
custom_getter=custom_getter)
File “/usr/local/lib/python2.7/dist-packages/tensorflow/python/ops/variable_scope.py”, line 345, in get_variable
validate_shape=validate_shape)
File “/usr/local/lib/python2.7/dist-packages/tensorflow/python/ops/variable_scope.py”, line 330, in _true_getter
caching_device=caching_device, validate_shape=validate_shape)
File “/usr/local/lib/python2.7/dist-packages/tensorflow/python/ops/variable_scope.py”, line 633, in _get_single_variable
name, “”.join(traceback.format_list(tb))))
ValueError: Variable v_stack0/v_W already exists, disallowed. Did you mean to set reuse=True in VarScope? Originally defined at:

File “generate.py”, line 57, in get_weights
W = tf.get_variable(name, shape, tf.float32, weights_initializer)
File “generate.py”, line 73, in gated_cnn
W_f = get_weights(W_shape, “v_W”, mask=mask)
File “generate.py”, line 107, in pixel_cnn
v_stack = gated_cnn([filter_size, filter_size, f_map], v_stack_in, mask=mask)

回复
tomwu说道：
2016年12月9日 下午3:03
这个是用对抗网络来做的吗？

回复
tomwu说道：
2016年12月9日 下午3:30
这个在variable_scope里面加上reuse=True应该就可以了，试一下

回复
张瑞说道：
2017年4月10日 上午9:32
请问下你这个问题解决了吗？我跑这个代码也出现了和你一样的问题

回复
study说道：
2017年4月10日 上午10:00
我跑这个代码也遇到了同样的问题，请问下你解决了吗？

回复
will说道：
2016年11月30日 下午9:29
求博主能不能给分享一下这个的数据集（生成妹子图）？真诚求数据集。本人学生，就是想学习一下，跑一下，不会用于商业用途。求分享数据集链接。

回复
blogadmin说道：
2016年11月30日 下午10:55
数据太大 有6g 不好传

回复
will说道：
2016年12月1日 上午10:38
能够分享一个百度云的链接？真诚感谢！

回复
wtf说道：
2016年12月1日 上午10:57
好吧 我传百度云上 稍等