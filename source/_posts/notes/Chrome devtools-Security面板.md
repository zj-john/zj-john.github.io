---
title: Chrome devtools-Security面板
categories:
  - notes
tags:
  - note
  - Chrome
  - devtools
toc: true
date: 2018-01-15 14:43:47
---
chrome大概是最常用的浏览器之一，这篇笔记讲解下Chrome devtools中Security面板的具体用法。  

[官方教程](https://developers.google.com/web/tools/chrome-devtools/)

<!-- more -->

# Security面板
使用安全面板调试混合内容问题，证书问题等等。

包含以下功能：
* 使用 Security Overview 可以立即查看当前页面是否安全。
* 检查各个源以查看连接和证书详情（安全源）或找出具体哪些请求未受保护（非安全源）。

## Security Overview

安全站点：
![](/images/security.png)

不安全站点：
![](/images/unsecurity.png)

* 如果请求的页面通过 HTTP 提供，则主源会被标记为不安全
* 如果请求的页面通过 HTTPS 检索，但页面会继续使用 HTTP 检索其他源的内容，然后页面仍然会被标记为不安全。这称为混合内容页面

## 检查源
* 点击安全源查看该源的连接和证书详情。
* 点击非安全源，Security 面板会提供 Network 面板过滤视图的链接
