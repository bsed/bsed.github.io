---
title: 调用tf.softmax_cross_entropy_with_logits函数出错解决
date: 2019-06-29 10:30:00
updated: 2019-11-03 15:20:56
tags: 
- python
- workon
- virtualenvwrapper
categories: 
- python

---
W0629 10:14:59.222113 139687708129088 deprecation_wrapper.py:119] From tf02_4.py:93: The name tf.nn.xw_plus_b is deprecated. Please use tf.compat.v1.nn.xw_plus_b instead.

W0629 10:14:59.223377 139687708129088 deprecation_wrapper.py:119] From tf02_4.py:100: The name tf.train.AdamOptimizer is deprecated. Please use tf.compat.v1.train.AdamOptimizer instead.

W0629 10:14:59.223525 139687708129088 deprecation.py:323] From tf02_4.py:101: softmax_cross_entropy_with_logits (from tensorflow.python.ops.nn_ops) is deprecated and will be removed in a future version.
Instructions for updating:


<!--more-->

```bash


Future major versions of TensorFlow will allow gradients to flow
into the labels input on backprop by default.

See `tf.nn.softmax_cross_entropy_with_logits_v2`.

Traceback (most recent call last):
  File "tf02_4.py", line 147, in <module>
    train_neural_network()
  File "tf02_4.py", line 101, in train_neural_network
    loss = tf.reduce_mean(tf.nn.softmax_cross_entropy_with_logits(output, Y))
  File "/home/kelvin/.venvs/lib/python3.7/site-packages/tensorflow/python/util/deprecation.py", line 324, in new_func
    return func(*args, **kwargs)
  File "/home/kelvin/.venvs/lib/python3.7/site-packages/tensorflow/python/ops/nn_ops.py", line 3238, in softmax_cross_entropy_with_logits
    logits)
  File "/home/kelvin/.venvs/lib/python3.7/site-packages/tensorflow/python/ops/nn_ops.py", line 2994, in _ensure_xent_args
    "named arguments (labels=..., logits=..., ...)" % name)
ValueError: Only call `softmax_cross_entropy_with_logits` with named arguments (labels=..., logits=..., ...)

```

![45569-86j0lgvlfmt.png](https://imgs.gnux.cn/usr/uploads/2019/06/1814067645.png)


只能使用命名参数的方式来调用。原来是这样的：
```python
tf.reduce_mean(tf.nn.softmax_cross_entropy_with_logits(output, Y))
```
因此需要修改wei：
```python
tf.reduce_sum(tf.nn.softmax_cross_entropy_with_logits(logits=output, labels=Y)))
```
demo:
```python
```

输出结果：
```bash
 command-line flag, not via TF_XLA_FLAGS) or set the envvar XLA_FLAGS=--xla_hlo_profile.
2019-06-29 10:20:36.307351: W tensorflow/core/framework/allocator.cc:107] Allocation of 331084800 exceeds 10% of system memory.
2019-06-29 10:20:36.415314: W tensorflow/core/framework/allocator.cc:107] Allocation of 330946560 exceeds 10% of system memory.
2019-06-29 10:20:36.415314: W tensorflow/core/framework/allocator.cc:107] Allocation of 330900480 exceeds 10% of system memory.
2019-06-29 10:20:36.415319: W tensorflow/core/framework/allocator.cc:107] Allocation of 330992640 exceeds 10% of system memory.
2019-06-29 10:20:38.517838: W tensorflow/core/framework/allocator.cc:107] Allocation of 330946560 exceeds 10% of system memory.
1.79717
准确率: 0.3
1.4893538
1.1656363
1.4993556
1.3560015
1.4799367
1.1544331
1.3337508
1.6067212
1.2456508
1.2202909
准确率: 0.3
1.1635814
1.2606294
1.007516
1.092985
1.2381994
1.2480145
0.96237624
1.1782404
0.9687604
1.0032924
准确率: 0.54
0.9313157
0.98196834
0.7944916
1.037979
0.93597674
0.85309213
1.0528121
1.057272
0.77165824
0.9028189
准确率: 0.58
0.94902337
0.8940152
0.95665336
0.9210044
0.9293932
0.780209
0.8196387
0.9663566
0.9278406
0.7516043
```