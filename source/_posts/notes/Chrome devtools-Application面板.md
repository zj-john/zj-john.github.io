---
title: Chrome devtools-Application面板
categories:
  - notes
tags:
  - Chrome
  - devtools
toc: true
date: 2018-01-09 18:04:47
---
chrome大概是最常用的浏览器之一，这篇笔记讲解下Chrome devtools中Application面板的具体用法。  

[官方教程](https://developers.google.com/web/tools/chrome-devtools/)

<!-- more -->

# Application面板
此面板检查加载的所有资源，包括IndexedDB与Web SQL数据库，本地和会话存储，cookie，应用程序缓存。

包含以下功能：
* 查看和修改本地存储与会话存储。
* 检查和修改 IndexedDB 数据库。
* 对 Web SQL 数据库执行语句。
* 查看应用缓存和服务工作线程缓存。
* 点击一次按钮即可清除所有存储、数据库、缓存和服务工作线程。

## 本地存储

Storage -> Local Storage 以键值对（KVP）存储

可以执行：
* 修改：双击
* 新增：空白处双击
* 删除：X 按钮
* 清空：Clear All
* 刷新

## Session存储

Storage -> Session Storage 以键值对（KVP）存储，操作同本地存储


## IndexedDB
Storage -> IndexedDB 以KVP存储的数据库，不支持新建库，支持数据的增删查改
![](/images/application_cache.png)


## Web SQL
<b>Web SQL Database规范已经被废弃</b>

Storage -> Web SQL Web SQL 数据库, 不支持值更新，支持使用sql语句查询。

[W3C教程](http://www.runoob.com/html/html5-web-sql.html)

## 应用缓存
Cache -> Application Cache

Type:
* Master:资源上指示此缓存为其主文件的 manifest 属性。
* Explicit:此资源在清单中明确列出。
* Network:指定此资源的清单必须来自网络。
* Fallback:Resource 列中的网址作为另一个网址（未在 DevTools 中显示）的回退网址形式列出。

状态：
* IDLE：缓存没有新更改。
* CHECKING：正在提取清单并检查有无更新。
* DOWNLOADING：正在将资源添加到缓存中。
* UPDATEREADY：存在新版本的缓存。
* OBSOLETE：正在删除缓存。

## 服务工作线程缓存
Cache -> Cache Storage

## 清除服务工作线程、存储、数据库和缓存
Application -> Clear Storage 选择性的注销服务工作线程、存储和缓存
