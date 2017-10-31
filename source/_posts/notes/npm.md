---
title: npm
date: 2017-09-13 15:26:39
categories:
  - notes
tags:
  - npm
  - cnpm
toc: true
---
# npm
## 描述
> nodejs的包管理器，用于node插件管理（包括安装、卸载、更新、查看、搜索、发布等）

## 教程
* [基础教程](http://www.runoob.com/nodejs/nodejs-npm.html/)  
* [官网教程](https://docs.npmjs.com)

<!-- more -->

## 常用命令
```Javascript
//npm初始化，在当前目录下生成package.json文件
npm init

//npm 升级
npm install npm -g

//安装模块
//--save是对生产环境所需依赖的声明(开发应用中使用的框架，库), 内容写入package.json
//--save-dev是对开发环境所需依赖的声明(构建工具，测试工具).内容写入package.json
npm install <Module Name> --save

// -g 全局安装，install可以简写为i
npm i <Module Name> -g

//目录下已有package.json，根据其中内容安装相关依赖
// 默认会下载dependencies和devDependencies中的模块
// 使用参数 --production或者注明NODE_ENV变量值为production时，只会下载dependencies中的模块
npm install

//先安装到全局，然后link到当前目录
npm link <Module Name>

// 查看所有已安装模块及其依赖 -g 列出所有全局安装的模块
npm ls
npm ls -g

// 卸载模块
npm uninstall <Module Name>  

// 更新模块
npm update <Module Name>  

//搜索模块
npm search <Module Name>

//展示模块信息
npm info <Module Name>

// 执行脚本、命令
// npm的start是一个特殊的脚本名称，不需要跟具体的脚本名称，
// 如果对应的此脚本的名称不是start，则用法如：npm run build.
// 相关脚本、命令可以在package.json中的scripts下写入。
npm start
npm run {script name}
```

本地安装
- 将安装包放在 ./node_modules 下（运行 npm 命令时所在的目录），如果没有 node_modules 目录，会在当前执行 npm 命令的目录下生成 node_modules 目录。
- 可以通过 require() 来引入本地安装的包。

全局安装
- 将安装包放在 /usr/local 下或者你 node 的安装目录。
- 可以直接在命令行里使用。

>如果你希望具备两者功能，则需要在两个地方安装它或使用 npm link。


# npm换源
## 淘宝npm镜像  
搜索地址：http://npm.taobao.org/  
registry地址：http://registry.npm.taobao.org/  

## 使用
有很多方法来配置npm的registry地址，下面根据不同情境列出几种比较常用的方法。  
以淘宝npm
镜像举例：  
1. 临时使用
```
npm --registry https://registry.npm.taobao.org install <Module Name>
```

2. 持久使用
```
npm config set registry https://registry.npm.taobao.org
// 配置后可通过下面方式来验证是否成功
npm config get registry
```
3. 通过cnpm使用
```
// 安装cnpm
npm install -g cnpm --registry=https://registry.npm.taobao.org
// 使用cnpm
cnpm install <Module Name>
```
