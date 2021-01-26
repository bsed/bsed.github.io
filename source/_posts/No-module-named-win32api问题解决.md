---
title: "No module named win32api问题解决"
categories: [ "Python" ]
tags: [ "python","win32api" ]
draft: false
slug: "no-module-named-win32api-problem-solving"
date: "2018-06-04 09:05:00"
---

```
E:\workspace\rust\emsdk>emsdk_env.bat
Failed to read environment variable PATH:
No module named win32api
Failed to read environment variable PATH:
No module named win32api
Traceback (most recent call last):
  File "E:\workspace\rust\emsdk\\emsdk", line 2342, in <module>
    sys.exit(main())
  File "E:\workspace\rust\emsdk\\emsdk", line 2237, in main
    env_string = construct_env(tools_to_activate, len(sys.argv) >= 3 and 'perm' in sys.argv[2])
  File "E:\workspace\rust\emsdk\\emsdk", line 1917, in construct_env
    newpath, added_path = adjusted_path(tools_to_activate)
  File "E:\workspace\rust\emsdk\\emsdk", line 1884, in adjusted_path
    existing_path = existing_path.split(ENVPATH_SEPARATOR)
AttributeError: 'NoneType' object has no attribute 'split'
```


<!--more-->


Python是没有自带访问windows系统API的库的，需要下载。库的名称叫pywin32，可以从网上直接下载。

以下链接地址可以下载：http://sourceforge.net/projects/pywin32/files%2Fpywin32/   （下载适合你的Python版本）

本文使用 pip 方式安装。
```bash
E:\workspace\rust\emsdk>pip install pywin32
Collecting pywin32
  Downloading https://files.pythonhosted.org/packages/a0/76/89b836562a36929bb913f9c17fca54fdd2c68b5e97ec0d626a389c087150/pywin32-223-cp27-cp27m-win32.whl (6.8MB)
    100% |████████████████████████████████| 6.8MB 156kB/s
Installing collected packages: pywin32
Successfully installed pywin32-223
You are using pip version 9.0.1, however version 10.0.1 is available.
You should consider upgrading via the 'python -m pip install --upgrade pip' command.
```

再次执行 `emsdk_env.bat`
```bash
E:\workspace\rust\emsdk>emsdk_env.bat
Adding directories to PATH:
PATH += E:\workspace\rust\emsdk

Setting environment variables:
EMSDK = E:/workspace/rust/emsdk
EM_CONFIG = C:\Users\Administrator\.emscripten
```