---
title: geth bug命令
categories:
  - notes
tags:
  - geth
  - bug
  - console
  - ethereum
toc: false
date: 2018-04-04 12:00:53
---

geth bug命令


<!-- more -->

这个命令功能很简单，可以快捷的向go-ethereum仓库提交报错。  

使用方式：  
终端输入以下命令，然后会自动打开一个浏览器窗口，跳转到go-ethereum的提issue的[地址](https://github.com/ethereum/go-ethereum/issues/new?body=Please+answer+these+questions+before+submitting+your+issue.+Thanks%21%0A%0A%23%23%23%23+What+did+you+do%3F%0A+%0A%23%23%23%23+What+did+you+expect+to+see%3F%0A+%0A%23%23%23%23+What+did+you+see+instead%3F%0A+%0A%23%23%23%23+System+details%0A%0AVersion%3A+1.8.2-stable%0AGo+Version%3A+go1.9.4%0AOS%3A+linux%0Auname+-sr%3A+Linux+4.13.0-38-generic%0ADistributor+ID%3A%09Ubuntu%0ADescription%3A%09Ubuntu+16.04.3+LTS%0ARelease%3A%0916.04%0ACodename%3A%09xenial%0A)。
```
> geth bug
```

提交issue页面上会默认带上你的环境信息，如下：

```
Please answer these questions before submitting your issue. Thanks!

#### What did you do?

#### What did you expect to see?

#### What did you see instead?

#### System details

Version: 1.8.2-stable
Go Version: go1.9.4
OS: linux
uname -sr: Linux 4.13.0-38-generic
Distributor ID:	Ubuntu
Description:	Ubuntu 16.04.3 LTS
Release:	16.04
Codename:	xenial
```
