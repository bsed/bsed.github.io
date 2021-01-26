---
title: "openssl升级导致的shadowsocks报错"
categories: [ "Linux" ]
tags: [ "shadowsocks" ]
draft: false
slug: "shadowsocks-error-caused-by-openssl-upgrade"
date: "2017-09-18 19:52:00"
---

错误如图：
![ss_error.png][1]


<!--more-->


```bash
kelvin@gnux:~> sudo sslocal -c cg.json                                                                                                                                                                   (09/18 19:4
INFO: loading config from cg.json
2017-09-18 19:46:54 INFO     loading libcrypto from libcrypto.so.1.1
Traceback (most recent call last):
  File "/usr/local/bin/sslocal", line 11, in <module>
    load_entry_point('shadowsocks==2.8.2', 'console_scripts', 'sslocal')()
  File "/usr/local/lib/python2.7/dist-packages/shadowsocks/local.py", line 39, in main
    config = shell.get_config(True)
  File "/usr/local/lib/python2.7/dist-packages/shadowsocks/shell.py", line 262, in get_config
    check_config(config, is_local)
  File "/usr/local/lib/python2.7/dist-packages/shadowsocks/shell.py", line 124, in check_config
    encrypt.try_cipher(config['password'], config['method'])
  File "/usr/local/lib/python2.7/dist-packages/shadowsocks/encrypt.py", line 44, in try_cipher
    Encryptor(key, method)
  File "/usr/local/lib/python2.7/dist-packages/shadowsocks/encrypt.py", line 83, in __init__
    random_string(self._method_info[1]))
  File "/usr/local/lib/python2.7/dist-packages/shadowsocks/encrypt.py", line 109, in get_cipher
    return m[2](method, key, iv, op)
  File "/usr/local/lib/python2.7/dist-packages/shadowsocks/crypto/openssl.py", line 76, in __init__
    load_openssl()
  File "/usr/local/lib/python2.7/dist-packages/shadowsocks/crypto/openssl.py", line 52, in load_openssl
    libcrypto.EVP_CIPHER_CTX_cleanup.argtypes = (c_void_p,)
  File "/usr/lib/python2.7/ctypes/__init__.py", line 379, in __getattr__
    func = self.__getitem__(name)
  File "/usr/lib/python2.7/ctypes/__init__.py", line 384, in __getitem__
    func = self._FuncPtr((name_or_ordinal, self))
AttributeError: /usr/lib/x86_64-linux-gnu/libcrypto.so.1.1: undefined symbol: EVP_CIPHER_CTX_cleanup
```

是因为openssl1.1.0f版本中，废弃了`EVP_CIPHER_CTX_cleanup`函数，可以用`EVP_CIPHER_CTX_reset`来代替此函数

此文件 ` sudo vim /usr/local/lib/python2.7/dist-packages/shadowsocks/crypto/openssl.py`中搜索所有的EVP_CIPHER_CTX_cleanup以EVP_CIPHER_CTX_reset代替即可，总共有两处。

最后贴出图。

```bash
kelvin@gnux:~> sudo sslocal -c cg.json 
INFO: loading config from cg.json
2017-09-18 19:47:48 INFO     loading libcrypto from libcrypto.so.1.1
2017-09-18 19:47:48 INFO     starting local at 127.0.0.1:1080
```


  [1]: https://imgs.gnux.cn/usr/uploads/2017/09/510086898.png