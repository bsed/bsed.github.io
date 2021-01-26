---
title: "TensorFlow练习11: 图像分类器 – retrain谷歌Inception模型"
categories: [ "大数据/NLP" ]
tags: [ "python","tensorflow","斗大的熊猫" ]
draft: false
slug: "tensorflow-exercise-11-image-classifier-retrain-google-inception-model"
date: "2019-09-17 16:12:00"
---

前一帖《[TensorFlow练习10: 实现谷歌Deep Dream](http://blog.topspeedsnail.com/archives/10667)》使用到了谷歌训练的Inception模型，本帖就基于Inception模型retrain一个图像分类器。

图像分类器应用广泛，连农业都在使用，如判断黄瓜种类。

本帖使用的训练数据是《[TensorFlow练习9: 生成妹子图（PixelCNN）](http://blog.topspeedsnail.com/archives/10660)》一文中使用的妹子图，最后训练出的分类器可以判断图片是不是妹子图。

首先下载tensorflow源代码：


<!--more-->


```bash
$ git clone https://github.com/tensorflow/tensorflow

$ git checkout r0.11  # checkout对应已安装的Tensorflow版本
```

在retrain自己的图像分类器之前，我们先来测试一下Google的Inception模型：

```bash
```

![bigcat.jpg][1]
![屏幕快照-2016-12-25-下午3.03.34.png][2]

- https://www.tensorflow.org/tutorials/image_recognition/

使用examples中的image_retraining。

训练：

```bash
$ python tensorflow/tensorflow/examples/image_retraining/retrain.py --bottleneck_dir bottleneck --how_many_training_steps 4000 --model_dir model --output_graph output_graph.pb --output_labels output_labels.txt --image_dir girl_types/

```

参数解释参考retrain.py源文件。

大概训练了半个小时：

![屏幕快照-2016-11-29-下午12.48.19.png][3]

生成的模型文件和labels文件：

![屏幕快照-2016-11-29-下午12.49.32.png][4]

使用训练好的模型：

```python
import tensorflow as tf

import sys

 

# 命令行参数，传入要判断的图片路径

image_file = sys.argv[1]

#print(image_file)

 

# 读取图像

image = tf.gfile.FastGFile(image_file, 'rb').read()

 

# 加载图像分类标签

labels = []

for label in tf.gfile.GFile("output_labels.txt"):

	labels.append(label.rstrip())

 

# 加载Graph

with tf.gfile.FastGFile("output_graph.pb", 'rb') as f:

	graph_def = tf.GraphDef()

	graph_def.ParseFromString(f.read())

	tf.import_graph_def(graph_def, name='')

 

with tf.Session() as sess:

	softmax_tensor = sess.graph.get_tensor_by_name('final_result:0')

	predict = sess.run(softmax_tensor, {'DecodeJpeg/contents:0': image})

 

	# 根据分类概率进行排序

	top = predict[0].argsort()[-len(predict[0]):][::-1]

	for index in top:

		human_string = labels[index]

		score = predict[0][index]

		print(human_string, score)
```

执行结果：

![屏幕快照-2016-11-29-下午1.05.47.png][5]

参考：

- https://www.tensorflow.org/versions/r0.9/how_tos/image_retraining/index.html
- [TensorFlow练习4: CNN, Convolutional Neural Networks](http://blog.topspeedsnail.com/archives/10451)
- [How Convolutional Neural Networks work](https://www.youtube.com/watch?v=FmpDIaiMIeA)


> **本文是对斗大的熊猫 tensorflow 系列 文章的备份。D啪乐嗯QQ: 193093683 **

  [1]: https://imgs.gnux.cn/usr/uploads/2019/09/2781121416.jpg
  [2]: https://imgs.gnux.cn/usr/uploads/2019/09/3066104437.png
  [3]: https://imgs.gnux.cn/usr/uploads/2019/09/2179897159.png
  [4]: https://imgs.gnux.cn/usr/uploads/2019/09/3101537132.png
  [5]: https://imgs.gnux.cn/usr/uploads/2019/09/3706730433.png