---
title: "TensorFlow练习13: 制作一个简单的聊天机器人"
categories: [ "大数据/NLP" ]
tags: [ "python","tensorflow","斗大的熊猫" ]
draft: false
slug: "tensorflow-exercise-13-making-a-simple-chat-robot"
date: "2019-09-19 16:22:00"
---

现在很多卖货公司都使用聊天机器人充当客服人员，许多科技巨头也纷纷推出各自的聊天助手，如苹果Siri、Google Now、Amazon Alexa、微软小冰等等。前不久有一个视频[比较了Google Now和Siri](https://www.youtube.com/watch?v=JFiu5rfnhzo)哪个更智能，貌似Google Now更智能。

本帖使用TensorFlow制作一个简单的聊天机器人。这个聊天机器人使用中文对话数据集进行训练（使用什么数据集训练决定了对话类型）。使用的模型为RNN(seq2seq)，和前文的《[RNN生成古诗词](http://blog.topspeedsnail.com/archives/10542)》《[RNN生成音乐](http://blog.topspeedsnail.com/archives/10508)》类似。

相关博文：


<!--more-->


- [使用深度学习打造智能聊天机器人](http://blog.csdn.net/malefactor/article/details/51901115)
- [脑洞大开：基于美剧字幕的聊天语料库建设方案](http://www.shareditor.com/blogshow/?blogId=105)
- [中文对白语料](https://github.com/rustch3n/dgk_lost_conv)
- https://www.tensorflow.org/versions/r0.12/tutorials/seq2seq/index.html
- https://github.com/tflearn/tflearn/blob/master/examples/nlp/lstm_generator_shakespeare.py

## 数据集

我使用现成的[影视对白数据集](https://github.com/rustch3n/dgk_lost_conv)，跪谢作者分享数据。

下载数据集：


```bash
$ wget https://raw.githubusercontent.com/rustch3n/dgk_lost_conv/master/dgk_shooter_min.conv.zip

# 解压

$ unzip dgk_shooter_min.conv.zip
```

数据预处理：

```python
import os

import random

 

conv_path = 'dgk_shooter_min.conv'

 

if not os.path.exists(conv_path):

	print('数据集不存在')

	exit()

 

# 数据集格式

"""

E

M 畹/华/吾/侄/

M 你/接/到/这/封/信/的/时/候/

M 不/知/道/大/伯/还/在/不/在/人/世/了/

E

M 咱/们/梅/家/从/你/爷/爷/起/

M 就/一/直/小/心/翼/翼/地/唱/戏/

M 侍/奉/宫/廷/侍/奉/百/姓/

M 从/来/不/曾/遭/此/大/祸/

M 太/后/的/万/寿/节/谁/敢/不/穿/红/

M 就/你/胆/儿/大/

M 唉/这/我/舅/母/出/殡/

M 我/不/敢/穿/红/啊/

M 唉/呦/唉/呦/爷/

M 您/打/得/好/我/该/打/

M 就/因/为/没/穿/红/让/人/赏/咱/一/纸/枷/锁/

M 爷/您/别/给/我/戴/这/纸/枷/锁/呀/

E

M 您/多/打/我/几/下/不/就/得/了/吗/

M 走/

M 这/是/哪/一/出/啊/…/ / /这/是/

M 撕/破/一/点/就/弄/死/你/

M 唉/

M 记/着/唱/戏/的/再/红/

M 还/是/让/人/瞧/不/起/

M 大/伯/不/想/让/你/挨/了/打/

M 还/得/跟/人/家/说/打/得/好/

M 大/伯/不/想/让/你/再/戴/上/那/纸/枷/锁/

M 畹/华/开/开/门/哪/

E

...

"""

 

# 我首先使用文本编辑器sublime把dgk_shooter_min.conv文件编码转为UTF-8，一下子省了不少麻烦

convs = []  # 对话集合

with open(conv_path, encoding = "utf8") as f:

	one_conv = []        # 一次完整对话

	for line in f:

		line = line.strip('\n').replace('/', '')

		if line == '':

			continue

		if line[0] == 'E':

			if one_conv:

				convs.append(one_conv)

			one_conv = []

		elif line[0] == 'M':

			one_conv.append(line.split(' ')[1])

"""

print(convs[:3])  # 个人感觉对白数据集有点不给力啊

[ ['畹华吾侄', '你接到这封信的时候', '不知道大伯还在不在人世了'], 

  ['咱们梅家从你爷爷起', '就一直小心翼翼地唱戏', '侍奉宫廷侍奉百姓', '从来不曾遭此大祸', '太后的万寿节谁敢不穿红', '就你胆儿大', '唉这我舅母出殡', '我不敢穿红啊', '唉呦唉呦爷', '您打得好我该打', '就因为没穿红让人赏咱一纸枷锁', '爷您别给我戴这纸枷锁呀'], 

  ['您多打我几下不就得了吗', '走', '这是哪一出啊 ', '撕破一点就弄死你', '唉', '记着唱戏的再红', '还是让人瞧不起', '大伯不想让你挨了打', '还得跟人家说打得好', '大伯不想让你再戴上那纸枷锁', '畹华开开门哪'], ....]

"""

 

# 把对话分成问与答

ask = []        # 问

response = []   # 答

for conv in convs:

	if len(conv) == 1:

		continue

	if len(conv) % 2 != 0:  # 奇数对话数, 转为偶数对话

		conv = conv[:-1]

	for i in range(len(conv)):

		if i % 2 == 0:

			ask.append(conv[i])

		else:

			response.append(conv[i])

 

"""

print(len(ask), len(response))

print(ask[:3])

print(response[:3])

['畹华吾侄', '咱们梅家从你爷爷起', '侍奉宫廷侍奉百姓']

['你接到这封信的时候', '就一直小心翼翼地唱戏', '从来不曾遭此大祸']

"""

 

def convert_seq2seq_files(questions, answers, TESTSET_SIZE = 8000):

    # 创建文件

    train_enc = open('train.enc','w')  # 问

    train_dec = open('train.dec','w')  # 答

    test_enc  = open('test.enc', 'w')  # 问

    test_dec  = open('test.dec', 'w')  # 答

 

    # 选择20000数据作为测试数据

    test_index = random.sample([i for i in range(len(questions))],TESTSET_SIZE)

 

    for i in range(len(questions)):

        if i in test_index:

            test_enc.write(questions[i]+'\n')

            test_dec.write(answers[i]+ '\n' )

        else:

            train_enc.write(questions[i]+'\n')

            train_dec.write(answers[i]+ '\n' )

        if i % 1000 == 0:

            print(len(range(len(questions))), '处理进度:', i)

 

    train_enc.close()

    train_dec.close()

    test_enc.close()

    test_dec.close()

 

convert_seq2seq_files(ask, response)

# 生成的*.enc文件保存了问题

# 生成的*.dec文件保存了回答
```

创建词汇表，然后把对话转为向量形式，参看练习1和7：


```python
# 前一步生成的问答文件路径

train_encode_file = 'train.enc'

train_decode_file = 'train.dec'

test_encode_file = 'test.enc'

test_decode_file = 'test.dec'

 

print('开始创建词汇表...')

# 特殊标记，用来填充标记对话

PAD = "__PAD__"

GO = "__GO__"

EOS = "__EOS__"  # 对话结束

UNK = "__UNK__"  # 标记未出现在词汇表中的字符

START_VOCABULART = [PAD, GO, EOS, UNK]

PAD_ID = 0

GO_ID = 1

EOS_ID = 2

UNK_ID = 3

# 参看tensorflow.models.rnn.translate.data_utils

 

vocabulary_size = 5000

# 生成词汇表文件

def gen_vocabulary_file(input_file, output_file):

	vocabulary = {}

	with open(input_file) as f:

		counter = 0

		for line in f:

			counter += 1

			tokens = [word for word in line.strip()]

			for word in tokens:

				if word in vocabulary:

					vocabulary[word] += 1

				else:

					vocabulary[word] = 1

		vocabulary_list = START_VOCABULART + sorted(vocabulary, key=vocabulary.get, reverse=True)

		# 取前5000个常用汉字, 应该差不多够用了(额, 好多无用字符, 最好整理一下. 我就不整理了)

		if len(vocabulary_list) > 5000:

			vocabulary_list = vocabulary_list[:5000]

		print(input_file + " 词汇表大小:", len(vocabulary_list))

		with open(output_file, "w") as ff:

			for word in vocabulary_list:

				ff.write(word + "\n")

 

gen_vocabulary_file(train_encode_file, "train_encode_vocabulary")

gen_vocabulary_file(train_decode_file, "train_decode_vocabulary")

 

train_encode_vocabulary_file = 'train_encode_vocabulary'

train_decode_vocabulary_file = 'train_decode_vocabulary'

 

print("对话转向量...")

# 把对话字符串转为向量形式

def convert_to_vector(input_file, vocabulary_file, output_file):

	tmp_vocab = []

	with open(vocabulary_file, "r") as f:

		tmp_vocab.extend(f.readlines())

	tmp_vocab = [line.strip() for line in tmp_vocab]

	vocab = dict([(x, y) for (y, x) in enumerate(tmp_vocab)])

	#{'硕': 3142, 'v': 577, 'Ｉ': 4789, '\ue796': 4515, '拖': 1333, '疤': 2201 ...}

	output_f = open(output_file, 'w')

	with open(input_file, 'r') as f:

		for line in f:

			line_vec = []

			for words in line.strip():

				line_vec.append(vocab.get(words, UNK_ID))

			output_f.write(" ".join([str(num) for num in line_vec]) + "\n")

	output_f.close()

 

convert_to_vector(train_encode_file, train_encode_vocabulary_file, 'train_encode.vec')

convert_to_vector(train_decode_file, train_decode_vocabulary_file, 'train_decode.vec')

 

convert_to_vector(test_encode_file, train_encode_vocabulary_file, 'test_encode.vec')

convert_to_vector(test_decode_file, train_decode_vocabulary_file, 'test_decode.vec')

```

生成的train_encode.vec和train_decode.vec用于训练，对应的词汇表是train_encode_vocabulary和train_decode_vocabulary。

## 训练

需要很长时间训练，这还是小数据集，如果用百GB级的数据，没10天半个月也训练不完。

使用的模型：[seq2seq_model.py](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/models/rnn/translate/seq2seq_model.py)。

代码：


```python
import tensorflow as tf  # 0.12

from tensorflow.models.rnn.translate import seq2seq_model

import os

import numpy as np

import math

 

PAD_ID = 0

GO_ID = 1

EOS_ID = 2

UNK_ID = 3

 

train_encode_vec = 'train_encode.vec'

train_decode_vec = 'train_decode.vec'

test_encode_vec = 'test_encode.vec'

test_decode_vec = 'test_decode.vec'

 

# 词汇表大小5000

vocabulary_encode_size = 5000

vocabulary_decode_size = 5000

 

buckets = [(5, 10), (10, 15), (20, 25), (40, 50)]

layer_size = 256  # 每层大小

num_layers = 3   # 层数

batch_size =  64

 

# 读取*dencode.vec和*decode.vec数据（数据还不算太多, 一次读人到内存）

def read_data(source_path, target_path, max_size=None):

	data_set = [[] for _ in buckets]

	with tf.gfile.GFile(source_path, mode="r") as source_file:

		with tf.gfile.GFile(target_path, mode="r") as target_file:

			source, target = source_file.readline(), target_file.readline()

			counter = 0

			while source and target and (not max_size or counter < max_size):

				counter += 1

				source_ids = [int(x) for x in source.split()]

				target_ids = [int(x) for x in target.split()]

				target_ids.append(EOS_ID)

				for bucket_id, (source_size, target_size) in enumerate(buckets):

					if len(source_ids) < source_size and len(target_ids) < target_size:

						data_set[bucket_id].append([source_ids, target_ids])

						break

				source, target = source_file.readline(), target_file.readline()

	return data_set

 

model = seq2seq_model.Seq2SeqModel(source_vocab_size=vocabulary_encode_size, target_vocab_size=vocabulary_decode_size,

                                   buckets=buckets, size=layer_size, num_layers=num_layers, max_gradient_norm= 5.0,

                                   batch_size=batch_size, learning_rate=0.5, learning_rate_decay_factor=0.97, forward_only=False)

 

config = tf.ConfigProto()

config.gpu_options.allocator_type = 'BFC'  # 防止 out of memory

 

with tf.Session(config=config) as sess:

	# 恢复前一次训练

	ckpt = tf.train.get_checkpoint_state('.')

	if ckpt != None:

		print(ckpt.model_checkpoint_path)

		model.saver.restore(sess, ckpt.model_checkpoint_path)

	else:

		sess.run(tf.global_variables_initializer())

 

	train_set = read_data(train_encode_vec, train_decode_vec)

	test_set = read_data(test_encode_vec, test_decode_vec)

 

	train_bucket_sizes = [len(train_set[b]) for b in range(len(buckets))]

	train_total_size = float(sum(train_bucket_sizes))

	train_buckets_scale = [sum(train_bucket_sizes[:i + 1]) / train_total_size for i in range(len(train_bucket_sizes))]

 

	loss = 0.0

	total_step = 0

	previous_losses = []

	# 一直训练，每过一段时间保存一次模型

	while True:

		random_number_01 = np.random.random_sample()

		bucket_id = min([i for i in range(len(train_buckets_scale)) if train_buckets_scale[i] > random_number_01])

 

		encoder_inputs, decoder_inputs, target_weights = model.get_batch(train_set, bucket_id)

		_, step_loss, _ = model.step(sess, encoder_inputs, decoder_inputs, target_weights, bucket_id, False)

 

		loss += step_loss / 500

		total_step += 1

 

		print(total_step)

		if total_step % 500 == 0:

			print(model.global_step.eval(), model.learning_rate.eval(), loss)

 

			# 如果模型没有得到提升，减小learning rate

			if len(previous_losses) > 2 and loss > max(previous_losses[-3:]):

				sess.run(model.learning_rate_decay_op)

			previous_losses.append(loss)

			# 保存模型

			checkpoint_path = "chatbot_seq2seq.ckpt"

			model.saver.save(sess, checkpoint_path, global_step=model.global_step)

			loss = 0.0

			# 使用测试数据评估模型

			for bucket_id in range(len(buckets)):

				if len(test_set[bucket_id]) == 0:

					continue

				encoder_inputs, decoder_inputs, target_weights = model.get_batch(test_set, bucket_id)

				_, eval_loss, _ = model.step(sess, encoder_inputs, decoder_inputs, target_weights, bucket_id, True)

				eval_ppx = math.exp(eval_loss) if eval_loss < 300 else float('inf')

				print(bucket_id, eval_ppx)
```



## 聊天机器人

使用训练好的模型：

```python
import tensorflow as tf  # 0.12

from tensorflow.models.rnn.translate import seq2seq_model

import os

import numpy as np

 

PAD_ID = 0

GO_ID = 1

EOS_ID = 2

UNK_ID = 3

 

train_encode_vocabulary = 'train_encode_vocabulary'

train_decode_vocabulary = 'train_decode_vocabulary'

 

def read_vocabulary(input_file):

	tmp_vocab = []

	with open(input_file, "r") as f:

		tmp_vocab.extend(f.readlines())

	tmp_vocab = [line.strip() for line in tmp_vocab]

	vocab = dict([(x, y) for (y, x) in enumerate(tmp_vocab)])

	return vocab, tmp_vocab

 

vocab_en, _, = read_vocabulary(train_encode_vocabulary)

_, vocab_de, = read_vocabulary(train_decode_vocabulary)

 

# 词汇表大小5000

vocabulary_encode_size = 5000

vocabulary_decode_size = 5000

 

buckets = [(5, 10), (10, 15), (20, 25), (40, 50)]

layer_size = 256  # 每层大小

num_layers = 3   # 层数

batch_size =  1

 

model = seq2seq_model.Seq2SeqModel(source_vocab_size=vocabulary_encode_size, target_vocab_size=vocabulary_decode_size,

                                   buckets=buckets, size=layer_size, num_layers=num_layers, max_gradient_norm= 5.0,

                                   batch_size=batch_size, learning_rate=0.5, learning_rate_decay_factor=0.99, forward_only=True)

model.batch_size = 1

 

with tf.Session() as sess:

	# 恢复前一次训练

	ckpt = tf.train.get_checkpoint_state('.')

	if ckpt != None:

		print(ckpt.model_checkpoint_path)

		model.saver.restore(sess, ckpt.model_checkpoint_path)

	else:

		print("没找到模型")

 

	while True:

		input_string = input('me > ')

		# 退出

		if input_string == 'quit':

			exit()

 

		input_string_vec = []

		for words in input_string.strip():

			input_string_vec.append(vocab_en.get(words, UNK_ID))

		bucket_id = min([b for b in range(len(buckets)) if buckets[b][0] > len(input_string_vec)])

		encoder_inputs, decoder_inputs, target_weights = model.get_batch({bucket_id: [(input_string_vec, [])]}, bucket_id)

		_, _, output_logits = model.step(sess, encoder_inputs, decoder_inputs, target_weights, bucket_id, True)

		outputs = [int(np.argmax(logit, axis=1)) for logit in output_logits]

		if EOS_ID in outputs:

			outputs = outputs[:outputs.index(EOS_ID)]

 

		response = "".join([tf.compat.as_str(vocab_de[output]) for output in outputs])

		print('AI > ' + response)
```


## 测试

![屏幕快照-2016-12-06-下午12.13.08.png](https://imgs.gnux.cn/usr/uploads/2019/09/2854668530.png)


额，好差劲。

上面的实现并没有用到任何自然语言的特性（分词、语法等等），只是单纯的使用数据强行提高它的“智商”。

后续练习：中文语音识别、文本转语音

> **本文是对斗大的熊猫tensorflow系列文章的备份。D啪乐嗯QQ: 193093683 **
