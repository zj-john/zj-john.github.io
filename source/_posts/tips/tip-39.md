---
title: node 判断运行环境
categories:
  - tips
tags:
  - node
  - os
toc: false
date: 2018-02-22 19:49:51
---
> 问题： node 判断运行环境


<!-- more -->
node中判断运行的环境，可以使用OS包或者process包的以下命令。
```
os.platform();
process.platform;
```
可用的取值有：
* linux
* win32: For Windows
* darwin: For OSX
* aix: IBM基于AT&T Unix System V开发的一套类UNIX操作系统
* freebsd: 类UNIX操作系统
* openbsd: 类UNIX操作系统
* sunos
* android: 这个取值目前在实验阶段
