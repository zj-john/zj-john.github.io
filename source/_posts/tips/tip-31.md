---
title: npm link 时各种Error
categories:
  - tips
tags:
  - npm
  - permission
toc: true
date: 2018-01-18 12:29:01
---
> 问题：npm link 时各种Error

开发npm包时，肯定会用到npm link功能。  
npm link可以让你在本地直接使用开发中的npm包，并且同步更新。

使用方法：

假如你开发的包的名字是test（package.json中的name对应的字段），然后有一个example目录，用来测试test的功能。

1. 在你开发的npm包的根目录下（有package.json的目录），命令行执行
```
npm link
```
这一步执行成功后，把test包连接到npm的全局模式下

2. 在example根目录下（同样有package.json文件的目录），命令行执行
```
npm link test
```
这一步执行成功后，你把全局的test包链接到了你本地目录下。  
这样在的你的example中可以直接import test包，而且npm包开发目录下的变动会同步反映到example下。

执行成功当然是最好的，但是执行时难免会遇到各种报错。

<!-- more -->

## 解决方案

执行过程中遇到了不少问题，no permission之类的，尝试了不少方法，最终解决是执行了以下几步：
1. 关闭打开此目录的IDE
2. 删除 node_module 目录
3. <b>删除package-lock.json 文件</b>
4. 执行需要的命令
