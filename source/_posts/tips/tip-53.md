---
title: python2.6安装pip
categories:
  - tips
tags:
  - python2.6
  - pip
toc: false
date: 2018-07-09 10:53:07
---

VPS上默认的python版本是2.6.6，按照网上安装pip时，报错如下：

```
Traceback (most recent call last):
  File "get-pi1p.py", line 20649, in <module>
    main()
  File "get-pi1p.py", line 197, in main
    bootstrap(tmpdir=tmpdir)
  File "get-pi1p.py", line 82, in bootstrap
    import pip._internal
  File "/tmp/tmp6hUd4Z/pip.zip/pip/_internal/__init__.py", line 42, in <module>
  File "/tmp/tmp6hUd4Z/pip.zip/pip/_internal/cmdoptions.py", line 16, in <module>
  File "/tmp/tmp6hUd4Z/pip.zip/pip/_internal/index.py", line 526
```

官方安装方法：
```
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
python get-pip.py
```

通过详细的查看官方说明，发现了这样一行说明
>The get-pip.py script is supported on the same python version as pip. For the now unsupported Python 2.6, alternate script is available [here](https://bootstrap.pypa.io/2.6/get-pip.py).


所以python2.6 下安装pip的正确安装方法是：
```
curl https://bootstrap.pypa.io/2.6/get-pip.py -o get-pip.py
python get-pip.py
```



# 参考：
* https://pip.pypa.io/en/stable/installing/
