---
title: 发布自己的npm包
date: 2018-01-29 14:13:24
categories:
  - notes
tags:
  - npm
toc: true
---
本文记录下如何发布一个自己的NPM包。

<!-- more -->

# 前提
阅读以下内容前，请先确保以下2个条件：
1. 环境依赖：  
依赖npm，所以需要已安装node.js。
2. 外部依赖：  
要发布的项目已经托管在公网的GitHub上，本地有对应的开发目录，假设为Test文件夹


# 1.注册NPM账号
[注册地址](https://www.npmjs.com/)  
注册成功后，在Test文件下运行以下命令：

> npm 账户关联是全局的，并不限制在当前文件夹下使用

```
npm adduser
Username: your name
Password: your password
Email: your email
```
执行成功后，则本地目录和npm账号已绑定关联。



>使用npm whoami查看当前关联账户，使用npm login来重新登录

# 2.发布npm包
重新审视下Test文件夹下的package.json文件，重点查看以下内容：
* name: 发布的npm包名称不允许重复，所以发布前在[npm网站](https://www.npmjs.com/)中搜索下相同名称的包是否存在。已存在则修改下名称
* version: 是否是一个恰当的版本来标识本次发布的内容
> 版本号：a.b.c。   
a是大版本号，用于当你做了不能向下兼容的修改时指定；  
b是次版本号，用于当你做了向下兼容的功能性新增时指定；  
c是修订号，用于当你做了向下兼容的问题修正时指定.  
可以使用npm version来指定版本，等同于修改package.json文件

* repository: 是否是你正确的GitHub项目地址

以上内容都确认无误，在Test文件夹下执行：
```
npm publish
```
命令成功，则npm发布完成。

Publish时报以下报错的解决办法：

* [You do not have permission to publish](https://zj-john.github.io/tips/cjc9zvmn20001tgf0a4v48mz6.html)
* [verified email required](https://zj-john.github.io/tips/cjc9zvmmv0000tgf056phihla.html)

# 后续
1. 开发：  
自己发布npm包后，在本地调试的方法是使用npm link做好关联，详见[文档](https://zj-john.github.io/tips/cjclj6x63000104f0ccof41sk.html)。
2. 持续集成：  
npm集成了travis，来在发布时测试你代码的可用性。详见[阮大文档](http://www.ruanyifeng.com/blog/2017/12/travis_ci_tutorial.html)
