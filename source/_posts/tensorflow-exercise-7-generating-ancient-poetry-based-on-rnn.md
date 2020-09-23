---
title: TensorFlow练习7: 基于RNN生成古诗词
date: 2019-09-13 15:37:00
updated: 2019-09-08 15:42:58
tags: 
- python
- tensorflow
- 斗大的熊猫
categories: 
- bigdata

---
RNN不像传统的神经网络-它们的输出输出是固定的，而RNN允许我们输入输出向量序列。RNN是为了对序列数据进行建模而产生的。

> 样本序列性：样本间存在顺序关系，每个样本和它之前的样本存在关联。比如说，在文本中，一个词和它前面的词是有关联的；在气象数据中，一天的气温和前几天的气温是有关联的。

例如本帖要使用RNN生成古诗，你给它输入一堆古诗词，它会学着生成和前面相关联的字词。如果你给它输入一堆姓名，它会学着生成姓名；给它输入一堆古典乐/歌词，它会学着生成古典乐/歌词，甚至可以给它输入源代码。

**关于RNN：**


<!--more-->


- [TensorFlow练习3: RNN, Recurrent Neural Networks](http://blog.topspeedsnail.com/archives/10443)
- http://karpathy.github.io/2015/05/21/rnn-effectiveness/

本帖代码移植自[char-rnn](https://github.com/karpathy/char-rnn)，它是基于Torch的洋文模型，稍加修改即可应用于中文。char-rnn使用文本文件做为输入、训练RNN模型，然后使用它生成和训练数据类似的文本。

**使用的数据集**：全唐诗(43030首)：https://pan.baidu.com/s/1o7QlUhO

训练：


```python
import collections

import numpy as np

import tensorflow as tf

 

#-------------------------------数据预处理---------------------------#

 

poetry_file ='poetry.txt'

 

# 诗集

poetrys = []

with open(poetry_file, "r", encoding='utf-8',) as f:

	for line in f:

		try:

			title, content = line.strip().split(':')

			content = content.replace(' ','')

			if '_' in content or '(' in content or '（' in content or '《' in content or '[' in content:

				continue

			if len(content) < 5 or len(content) > 79:

				continue

			content = '[' + content + ']'

			poetrys.append(content)

		except Exception as e: 

			pass

 

# 按诗的字数排序

poetrys = sorted(poetrys,key=lambda line: len(line))

print('唐诗总数: ', len(poetrys))

 

# 统计每个字出现次数

all_words = []

for poetry in poetrys:

	all_words += [word for word in poetry]

counter = collections.Counter(all_words)

count_pairs = sorted(counter.items(), key=lambda x: -x[1])

words, _ = zip(*count_pairs)

 

# 取前多少个常用字

words = words[:len(words)] + (' ',)

# 每个字映射为一个数字ID

word_num_map = dict(zip(words, range(len(words))))

# 把诗转换为向量形式，参考TensorFlow练习1

to_num = lambda word: word_num_map.get(word, len(words))

poetrys_vector = [ list(map(to_num, poetry)) for poetry in poetrys]

#[[314, 3199, 367, 1556, 26, 179, 680, 0, 3199, 41, 506, 40, 151, 4, 98, 1],

#[339, 3, 133, 31, 302, 653, 512, 0, 37, 148, 294, 25, 54, 833, 3, 1, 965, 1315, 377, 1700, 562, 21, 37, 0, 2, 1253, 21, 36, 264, 877, 809, 1]

#....]

 

# 每次取64首诗进行训练

batch_size = 64

n_chunk = len(poetrys_vector) // batch_size

x_batches = []

y_batches = []

for i in range(n_chunk):

	start_index = i * batch_size

	end_index = start_index + batch_size

 

	batches = poetrys_vector[start_index:end_index]

	length = max(map(len,batches))

	xdata = np.full((batch_size,length), word_num_map[' '], np.int32)

	for row in range(batch_size):

		xdata[row,:len(batches[row])] = batches[row]

	ydata = np.copy(xdata)

	ydata[:,:-1] = xdata[:,1:]

	"""

	xdata             ydata

	[6,2,4,6,9]       [2,4,6,9,9]

	[1,4,2,8,5]       [4,2,8,5,5]

	"""

	x_batches.append(xdata)

	y_batches.append(ydata)

 

 

#---------------------------------------RNN--------------------------------------#

 

input_data = tf.placeholder(tf.int32, [batch_size, None])

output_targets = tf.placeholder(tf.int32, [batch_size, None])

# 定义RNN

def neural_network(model='lstm', rnn_size=128, num_layers=2):

	if model == 'rnn':

		cell_fun = tf.nn.rnn_cell.BasicRNNCell

	elif model == 'gru':

		cell_fun = tf.nn.rnn_cell.GRUCell

	elif model == 'lstm':

		cell_fun = tf.nn.rnn_cell.BasicLSTMCell

 

	cell = cell_fun(rnn_size, state_is_tuple=True)

	cell = tf.nn.rnn_cell.MultiRNNCell([cell] * num_layers, state_is_tuple=True)

 

	initial_state = cell.zero_state(batch_size, tf.float32)

 

	with tf.variable_scope('rnnlm'):

		softmax_w = tf.get_variable("softmax_w", [rnn_size, len(words)+1])

		softmax_b = tf.get_variable("softmax_b", [len(words)+1])

		with tf.device("/cpu:0"):

			embedding = tf.get_variable("embedding", [len(words)+1, rnn_size])

			inputs = tf.nn.embedding_lookup(embedding, input_data)

 

	outputs, last_state = tf.nn.dynamic_rnn(cell, inputs, initial_state=initial_state, scope='rnnlm')

	output = tf.reshape(outputs,[-1, rnn_size])

 

	logits = tf.matmul(output, softmax_w) + softmax_b

	probs = tf.nn.softmax(logits)

	return logits, last_state, probs, cell, initial_state

#训练

def train_neural_network():

	logits, last_state, _, _, _ = neural_network()

	targets = tf.reshape(output_targets, [-1])

	loss = tf.nn.seq2seq.sequence_loss_by_example([logits], [targets], [tf.ones_like(targets, dtype=tf.float32)], len(words))

	cost = tf.reduce_mean(loss)

	learning_rate = tf.Variable(0.0, trainable=False)

	tvars = tf.trainable_variables()

	grads, _ = tf.clip_by_global_norm(tf.gradients(cost, tvars), 5)

	optimizer = tf.train.AdamOptimizer(learning_rate)

	train_op = optimizer.apply_gradients(zip(grads, tvars))

 

	with tf.Session() as sess:

		sess.run(tf.initialize_all_variables())

 

		saver = tf.train.Saver(tf.all_variables())

 

		for epoch in range(50):

			sess.run(tf.assign(learning_rate, 0.002 * (0.97 ** epoch)))

			n = 0

			for batche in range(n_chunk):

				train_loss, _ , _ = sess.run([cost, last_state, train_op], feed_dict={input_data: x_batches[n], output_targets: y_batches[n]})

				n += 1

				print(epoch, batche, train_loss)

			if epoch % 7 == 0:

				saver.save(sess, 'poetry.module', global_step=epoch)

 

train_neural_network()
```

使用训练好的模型生成古诗：


```python

import collections

import numpy as np

import tensorflow as tf

 

#-------------------------------数据预处理---------------------------#

 

poetry_file ='poetry.txt'

 

# 诗集

poetrys = []

with open(poetry_file, "r", encoding='utf-8',) as f:

	for line in f:

		try:

			title, content = line.strip().split(':')

			content = content.replace(' ','')

			if '_' in content or '(' in content or '（' in content or '《' in content or '[' in content:

				continue

			if len(content) < 5 or len(content) > 79:

				continue

			content = '[' + content + ']'

			poetrys.append(content)

		except Exception as e: 

			pass

 

# 按诗的字数排序

poetrys = sorted(poetrys,key=lambda line: len(line))

print('唐诗总数: ', len(poetrys))

 

# 统计每个字出现次数

all_words = []

for poetry in poetrys:

	all_words += [word for word in poetry]

counter = collections.Counter(all_words)

count_pairs = sorted(counter.items(), key=lambda x: -x[1])

words, _ = zip(*count_pairs)

 

# 取前多少个常用字

words = words[:len(words)] + (' ',)

# 每个字映射为一个数字ID

word_num_map = dict(zip(words, range(len(words))))

# 把诗转换为向量形式，参考TensorFlow练习1

to_num = lambda word: word_num_map.get(word, len(words))

poetrys_vector = [ list(map(to_num, poetry)) for poetry in poetrys]

#[[314, 3199, 367, 1556, 26, 179, 680, 0, 3199, 41, 506, 40, 151, 4, 98, 1],

#[339, 3, 133, 31, 302, 653, 512, 0, 37, 148, 294, 25, 54, 833, 3, 1, 965, 1315, 377, 1700, 562, 21, 37, 0, 2, 1253, 21, 36, 264, 877, 809, 1]

#....]

 

batch_size = 1

n_chunk = len(poetrys_vector) // batch_size

x_batches = []

y_batches = []

for i in range(n_chunk):

	start_index = i * batch_size

	end_index = start_index + batch_size

 

	batches = poetrys_vector[start_index:end_index]

	length = max(map(len,batches))

	xdata = np.full((batch_size,length), word_num_map[' '], np.int32)

	for row in range(batch_size):

		xdata[row,:len(batches[row])] = batches[row]

	ydata = np.copy(xdata)

	ydata[:,:-1] = xdata[:,1:]

	"""

	xdata             ydata

	[6,2,4,6,9]       [2,4,6,9,9]

	[1,4,2,8,5]       [4,2,8,5,5]

	"""

	x_batches.append(xdata)

	y_batches.append(ydata)

 

 

#---------------------------------------RNN--------------------------------------#

 

input_data = tf.placeholder(tf.int32, [batch_size, None])

output_targets = tf.placeholder(tf.int32, [batch_size, None])

# 定义RNN

def neural_network(model='lstm', rnn_size=128, num_layers=2):

	if model == 'rnn':

		cell_fun = tf.nn.rnn_cell.BasicRNNCell

	elif model == 'gru':

		cell_fun = tf.nn.rnn_cell.GRUCell

	elif model == 'lstm':

		cell_fun = tf.nn.rnn_cell.BasicLSTMCell

 

	cell = cell_fun(rnn_size, state_is_tuple=True)

	cell = tf.nn.rnn_cell.MultiRNNCell([cell] * num_layers, state_is_tuple=True)

 

	initial_state = cell.zero_state(batch_size, tf.float32)

 

	with tf.variable_scope('rnnlm'):

		softmax_w = tf.get_variable("softmax_w", [rnn_size, len(words)+1])

		softmax_b = tf.get_variable("softmax_b", [len(words)+1])

		with tf.device("/cpu:0"):

			embedding = tf.get_variable("embedding", [len(words)+1, rnn_size])

			inputs = tf.nn.embedding_lookup(embedding, input_data)

 

	outputs, last_state = tf.nn.dynamic_rnn(cell, inputs, initial_state=initial_state, scope='rnnlm')

	output = tf.reshape(outputs,[-1, rnn_size])

 

	logits = tf.matmul(output, softmax_w) + softmax_b

	probs = tf.nn.softmax(logits)

	return logits, last_state, probs, cell, initial_state

 

#-------------------------------生成古诗---------------------------------#

# 使用训练完成的模型

 

def gen_poetry():

	def to_word(weights):

		t = np.cumsum(weights)

		s = np.sum(weights)

		sample = int(np.searchsorted(t, np.random.rand(1)*s))

		return words[sample]

 

	_, last_state, probs, cell, initial_state = neural_network()

 

	with tf.Session() as sess:

		sess.run(tf.initialize_all_variables())

 

		saver = tf.train.Saver(tf.all_variables())

		saver.restore(sess, 'poetry.module-49')

 

		state_ = sess.run(cell.zero_state(1, tf.float32))

 

		x = np.array([list(map(word_num_map.get, '['))])

		[probs_, state_] = sess.run([probs, last_state], feed_dict={input_data: x, initial_state: state_})

		word = to_word(probs_)

		#word = words[np.argmax(probs_)]

		poem = ''

		while word != ']':

			poem += word

			x = np.zeros((1,1))

			x[0,0] = word_num_map[word]

			[probs_, state_] = sess.run([probs, last_state], feed_dict={input_data: x, initial_state: state_})

			word = to_word(probs_)

			#word = words[np.argmax(probs_)]

		return poem

 

print(gen_poetry())
```

生成的“诗词”（至少格式对上了）：

```
新犬随风起，一璃迹阵悲。

浅昏罢庄哉，清插去园空。

双叶坐成鉴，王妓水正苑。

鸟声不成影，胙滩朱瓮声。

无斑红芜踏，那期日正闲。

吾燕登无士，无处得赵名。

 

并灭图微蒿，淮头水十荔。

晴花尚乘望，官宽留可求。

最忆青州守，英仍临阳峰。

生人隔天道，在国思山田。

登临闭石土，阵下一欢娱。

林暝今又少，孙频唯在愁。
```

生成藏头诗：


```python
def gen_poetry_with_head(head):

	def to_word(weights):

		t = np.cumsum(weights)

		s = np.sum(weights)

		sample = int(np.searchsorted(t, np.random.rand(1)*s))

		return words[sample]

 

	_, last_state, probs, cell, initial_state = neural_network()

 

	with tf.Session() as sess:

		sess.run(tf.initialize_all_variables())

 

		saver = tf.train.Saver(tf.all_variables())

		saver.restore(sess, 'poetry.module-49')

 

		state_ = sess.run(cell.zero_state(1, tf.float32))

		poem = ''

		i = 0

		for word in head:

			while word != '，' and word != '。':

				poem += word

				x = np.array([list(map(word_num_map.get, word))])

				[probs_, state_] = sess.run([probs, last_state], feed_dict={input_data: x, initial_state: state_})

				word = to_word(probs_)

				time.sleep(1)

			if i % 2 == 0:

				poem += '，'

			else:

				poem += '。'

			i += 1

		return poem

 

print(gen_poetry_with_head('一二三四'))
```

上面使用的TensroFlow版本为0.11，0.12版本貌似不能直接运行，简单修改如下代码：

读取模型的方法：

```python
module_file = tf.train.latest_checkpoint('.')

	#print(module_file)

	saver.restore(sess, module_file)
```

tf.initialize_all_variables()  deprecated，使用tf.global_variables_initializer()替代。

- https://github.com/ryankiros/neural-storyteller（根据图片生成故事）

## 问题

《TensorFlow练习7: 基于RNN生成古诗词》有49个想法
chungang说道：
2017年7月14日 下午5:08
raceback (most recent call last):
File “poetry.py”, line 143, in
print(gen_poetry())
File “poetry.py”, line 130, in gen_poetry
[probs_, state_] = sess.run([probs, last_state], feed_dict={input_data: x, initial_state: state_})
File “/usr/local/lib/python2.7/dist-packages/tensorflow/python/client/session.py”, line 789, in run
run_metadata_ptr)
File “/usr/local/lib/python2.7/dist-packages/tensorflow/python/client/session.py”, line 975, in _run
% (np_val.shape, subfeed_t.name, str(subfeed_t.get_shape())))
ValueError: Cannot feed value of shape (1, 1) for Tensor u’Placeholder:0′, which has shape ‘(64, ?)’

回复
hitdagang说道：
2017年7月8日 下午9:09
楼主，在运行代码出现：
cell_fun = tf.nn.rnn_cell.BasicLSTMCell
builtins.AttributeError: module ‘tensorflow.python.ops.nn’ has no attribute ‘rnn_cell’
请问这是什么原因？

回复
hackerxuan说道：
2017年7月8日 上午11:59
在训练模型的98行tf.nn.dynamic_rnn报builtins.ValueError: Attempt to reuse RNNCell
楼主这个问题应该怎么解决呢？tf 1.1.0刚入坑

回复
devlab说道：
2017年6月29日 下午4:40
海湖水鸟起，月半莫知情。
不得池亭扁，射荒恨有谁。
繁华微岐下，埋客雨烧来。
涧街君相贺，书迥尽任香。
苍苍闲自步，当里行关东。
良地蹄轮约，斋山心不飞。
如吾向南去，后下讵能归。

回复
陈说道：
2017年6月12日 下午9:00
博主，你好。我模型生成后，运行生成“诗歌”代码时，
在第125行的gen_poetry()函数中的
[probs_, state_] = sess.run([probs, last_state], feed_dict={input_data: x, initial_state: state_})

出现Cannot feed value of shape (1, 1) for Tensor ‘Placeholder:0’, which has shape ‘(64, ?)’ 错误，运行生成“诗歌”代码时，需要对输入x进行怎样的处理啊？

回复
湖心小笨酸说道：
2017年5月12日 下午7:38
tensorflow.python.framework.errors_impl.NotFoundError: Unsuccessful TensorSliceReader constructor: Failed to find any matching files for poetry.module-49
这个是怎么回事？

回复
徐说道：
2017年5月11日 下午4:54
我的是1.0.0版本，训练时报错了AttributeError: module ‘tensorflow.python.ops.nn’ has no attribute ‘rnn_cell’ 这个应该怎么改

回复
徐说道：
2017年5月11日 下午5:23
我知道怎么改了

回复
mm说道：
2017年5月16日 上午10:27
能否告知一下

回复
wjw说道：
2017年6月11日 上午12:53
麻烦问下如何修改

回复
dddwe_说道：
2017年5月5日 上午10:58
print(saver.restore(sess, ‘poetry.module-49’)) 为什么是None。有文件保存的

回复
xiaoxu_2说道：
2017年5月5日 上午9:56
Traceback (most recent call last):
File “D:/pycode/tensorflow/analy_test/TensorFlow_flight/word2vec_/quickly_test.py”, line 180, in
print(gen_poetry())
File “D:/pycode/tensorflow/analy_test/TensorFlow_flight/word2vec_/quickly_test.py”, line 166, in gen_poetry
initial_state: state_})
File “C:\Users\Lenovo\AppData\Local\Programs\Python\Python35\lib\site-packages\tensorflow\python\client\session.py”, line 767, in run
run_metadata_ptr)
File “C:\Users\Lenovo\AppData\Local\Programs\Python\Python35\lib\site-packages\tensorflow\python\client\session.py”, line 938, in _run
np_val = np.asarray(subfeed_val, dtype=subfeed_dtype)
File “C:\Users\Lenovo\AppData\Local\Programs\Python\Python35\lib\site-packages\numpy\core\numeric.py”, line 531, in asarray
return array(a, dtype, copy=False, order=order)
TypeError: int() argument must be a string, a bytes-like object or a number, not ‘NoneType’

回复
FancyVin说道：
2017年4月2日 上午11:35
哪位老哥能再帮忙搞个数据集呀~630074105@qq.com

回复
张瑞说道：
2017年3月30日 上午10:52
请问下 这个报错是什么原因啊
W tensorflow/core/framework/op_kernel.cc:975] Not found: Unsuccessful TensorSliceReader constructor: Failed to find any matching files for poetry.module-49
W tensorflow/core/framework/op_kernel.cc:975] Not found: Unsuccessful TensorSliceReader constructor: Failed to find any matching files for poetry.module-49
W tensorflow/core/framework/op_kernel.cc:975] Not found: Unsuccessful TensorSliceReader constructor: Failed to find any matching files for poetry.module-49
W tensorflow/core/framework/op_kernel.cc:975] Not found: Unsuccessful TensorSliceReader constructor: Failed to find any matching files for poetry.module-49
W tensorflow/core/framework/op_kernel.cc:975] Not found: Unsuccessful TensorSliceReader constructor: Failed to find any matching files for poetry.module-49
W tensorflow/core/framework/op_kernel.cc:975] Not found: Unsuccessful TensorSliceReader constructor: Failed to find any matching files for poetry.module-49
W tensorflow/core/framework/op_kernel.cc:975] Not found: Unsuccessful TensorSliceReader constructor: Failed to find any matching files for poetry.module-49

回复
xiaoyu_wang说道：
2017年5月2日 下午7:46
文件名前面加./

回复
湖心小笨酸说道：
2017年5月13日 上午10:21
Unable to open table file .\poetry.module-49: Data loss: file is too short to be an sstable: perhaps your file is in a different file format
这个怎么改

回复
garbo说道：
2017年6月22日 上午10:05
我在运行tensorflow15的测试时，同样出现了这个问题，请问是什么原因

回复
Mr田说道：
2017年3月13日 下午4:41
锅锅，数据集又挂了，帮忙修复下网盘链接啊~

回复
峰峰说道：
2017年2月25日 上午12:09
大神，请问这是什么问题 用什么办法解决 ValueError: Variable rnnlm/softmax_w already exists, disallowed. Did you mean to set reuse=True in VarScope? Originally defined at:

回复
Mr田说道：
2017年3月13日 下午4:49
求训练数据集…313534402@qq.com

回复
Mr田说道：
2017年3月14日 下午5:41
有热心的大哥发我训练集了，谢谢啦~

回复
dx小新说道：
2017年3月15日 下午8:44
改好数值后，jupyter中用restart就好了

回复
王雯说道：
2017年3月20日 下午1:52
我也出现了这个问题，请问你最后是怎么解决的？

回复
毛丽丽说道：
2017年6月23日 下午3:39
请问问题解决了么？我也遇到了这个问题

回复
fangning说道：
2017年2月6日 下午5:05
古诗词的百度云盘好像失效了，能发邮件给我诗词数据吗？谢谢

回复
fangning说道：
2017年2月6日 下午7:36
已经可以下载了，:-)

回复
张宏伦说道：
2017年2月23日 下午1:51
链接又失效了，不能愉快地玩耍了

回复
secha说道：
2017年1月10日 下午3:37
Traceback (most recent call last):
File “/home/scj/doc/tf/poetry/poetry_rnn2.py”, line 155, in
print(gen_poetry())
File “/home/scj/doc/tf/poetry/poetry_rnn2.py”, line 141, in gen_poetry
[probs_, state_] = sess.run([probs, last_state], feed_dict={input_data: x, initial_state: state_})
File “/home/scj/.local/lib/python3.5/site-packages/tensorflow/python/client/session.py”, line 766, in run
run_metadata_ptr)
File “/home/scj/.local/lib/python3.5/site-packages/tensorflow/python/client/session.py”, line 943, in _run
% (np_val.shape, subfeed_t.name, str(subfeed_t.get_shape())))
ValueError: Cannot feed value of shape (1, 1) for Tensor ‘Placeholder:0’, which has shape ‘(64, ?)’
这个怎样修改。

回复
张力说道：
2017年3月14日 下午3:59
我也有相同的问题, 我用的是 tensorflow 0.11.0

回复
Xuxue说道：
2017年3月14日 下午5:04
你需要吧batch_size改成1 不要

回复
zxy说道：
2017年5月3日 上午2:23
你是怎样解决这个问题的

回复
hy0kl说道：
2017年1月6日 下午3:52
丛焰长辙妒，此去遍三年。
我筒终无力，阴中有旧游。
西百竹中欲，草转五天松。

归意园鱼去，钵旨作寺情。
日正谷林外，风来达授还。
从兹储舆绕，新装莫堪泛。

就是没有意义的唐诗

回复
Tsflow说道：
2017年3月9日 下午9:22
你好，方便留个联系方式吗？向你学习一下，谢谢！

回复
hsinyang说道：
2017年4月10日 下午2:39
请问训练模型大约需要多久的时间？

回复
fxq说道：
2016年12月27日 下午8:08
藏头诗的代码有bug，跑不出来

回复
tfw说道：
2016年12月27日 下午9:19
训练数据少

回复
Kelsey_H说道：
2016年12月26日 下午6:54
你好，我想问一下，最后生成古诗的时候的“to_word(weights)函数”的意义。我的理解中，这个函数是在随机生成下一个字，貌似和网络输出的概率没有什么关系。那么这个函数又是如何体现前一个字和后一个的关系的呢？

回复
毛丽丽说道：
2017年6月24日 下午6:07
请问这个问题搞明白了么？我也没想通，这块是什么意思》

回复
toddler说道：
2016年12月21日 下午11:29
博主，对saver.restore(sess, module_file)会有TypeError: expected bytes, NoneType found报错？

回复
徐说道：
2017年5月12日 下午6:53
Failed to find any matching files for poetry.test我报了这个错

回复
luyee2010说道：
2016年11月25日 上午1:55
感谢博主，成功生成古诗词：
不?期花起怔，紲陼赏闲魗。之生头綽兰，门间花寒松
盖贯下于掌，碫何香徆櫘。抮入齏几里，客仙青归路
乆有谢州别，寒屝暮畦出。孯更春花雪，难随萑剆初。
火摃将淯晗堀，孤何白山晛来。院少芳娘水，从作向移新?

回复
倾风随雨说道：
2016年11月24日 下午4:42
站长，from tensorflow.python.ops import rnn_cell
ImportError: cannot import name rnn_cell
请问是TF版本太低了吗？有什么方法解决？谢谢

回复
耿佳佳说道：
2016年11月24日 下午8:30
tf0.11已经改了，tf.nn.rnn_cell

回复
Tsflow说道：
2017年3月14日 下午12:04
请问tf 1.0版本的，怎么修改代码？请指点，谢谢！

回复
hooflow说道：
2017年3月22日 下午2:19
你有报什么错么

回复
彭说道：
2017年4月25日 下午5:22
你解决了没 ？我也是1.0.1版本

回复
Snow说道：
2017年5月26日 上午9:58
rnn在tf.contrib,import rnn之后，使用rnn.BasicRNNCell

karl说道：
2016年11月24日 下午8:32
rnncell改到tf.nn里面了，参看文档

回复
hitdagang说道：
2017年7月8日 下午9:11
cell_fun = tf.nn.rnn_cell.BasicLSTMCell
builtins.AttributeError: module ‘tensorflow.python.ops.nn’ has no attribute ‘rnn_cell’