---
title: 微前端实践
date: 2020-04-24 15:59:34
categories:
  - projects
tags:
  - micro frontend
  - single-spa
  - qiankun
toc: true
---



<!-- more -->
# 微前端

微前端是2019年很火的一个话题，但是这个概念其实是2016年提出的。
主要解决前端巨石项目的问题。

1. 技术栈升级
2. 独立开发/维护/测试（沙盒）
3. 增量打包/发布

挂载应用到不同的dom上


# 主要问题
1. 路由处理
2. 状态处理
3. 打包/部署
4. CSS污染

# 框架
## single-spa

## qiankun

# 技术实践

## 步骤
> 采用不同子项目在同一个git仓库的方式

1. react基础应用

React16 + ReactRouter5


2. 切割应用
待做：通过脚本实现分离

独立成单独项目，拥有自己的package.json，webpack

* 创建3个子项目：npx create-react-app home/work/main
* 去除项目中的无关项，public目录是共有的，也去除。
* 为每个项目设置单独的webpack（你也可以使用不同的devserver方式和构建方式，推荐使用webpack）
* webpack及其插件，可以安装在最外层

3. 各个框架适配

## single-spa 最小demo

## single-spa react demo

## 抛弃single-spa

# 延伸
每一个独立组件或者说JS的模块就是一个应用，用调用service的方法使用组件

