---
title: "tensorflow_gpu-1.1.0-cp36-cp36m-linux_x86_64.whl is not a supported wheel on this platform"
categories: [ "大数据/NLP" ]
tags: [ "python","tensorflow" ]
draft: false
slug: "tensorflowgpu110cp36cp36mlinuxx8664whl-is-not-a-supported-wheel-on-this-platform"
date: "2019-06-17 21:02:00"
---

```bash
kelvin@mb  ~/workspace/python
$ export TF_BINARY_URL=https://storage.googleapis.com/tensorflow/linux/gpu/tensorflow_gpu-1.1.0-cp36-cp36m-linux_x86_64.whl
(tf) 
kelvin@mb  ~/workspace/python
$ pip3 install --upgrade $TF_BINARY_URL
ERROR: tensorflow_gpu-1.1.0-cp36-cp36m-linux_x86_64.whl is not a supported wheel on this platform.
(tf) 
```
![90927-njuwqzpg8hc.png](https://imgs.gnux.cn/usr/uploads/2019/06/3375961124.png)
Tensorflow1.13版本将正式支持Python3.7。

注意：同时需要把NumPy升级到最新版本
```bash
pip install tensorflow-gpu==1.13.1
pip install numpy --upgrade
```
非GPU 使用 `pip install tensorflow==1.13.1`
经测试，Mac、Windows、Linux下都可以正常运行。

 - [https://www.jianshu.com/p/1a3e194886b4](https://www.jianshu.com/p/1a3e194886b4)
 - [https://github.com/tensorflow/tensorflow/tags](https://github.com/tensorflow/tensorflow/tags)