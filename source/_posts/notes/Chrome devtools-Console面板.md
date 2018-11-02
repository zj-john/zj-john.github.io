---
title: Chrome devtools-Console面板
categories:
  - notes
tags:
  - Chrome
  - devtools
toc: true
date: 2017-12-27 10:14:03
---
chrome大概是最常用的浏览器之一，这篇笔记讲解下Chrome devtools中Console面板的具体用法。  

[官方教程](https://developers.google.com/web/tools/chrome-devtools/)

<!-- more -->

## Console面板
在开发期间，可以使用控制台面板记录诊断信息，或者使用它作为 shell在页面上与JavaScript交互。

### 在其他面板以抽屉式展示
* 在 DevTools 处于聚焦状态时按 Esc。
* 按 Customize and control DevTools 按钮，然后按 Show console。


### 消息堆叠
如果一条消息连续重复，而不是在新行上输出每一个消息实例，控制台将“堆叠”消息并在左侧外边距显示一个数字。此数字表示该消息已重复的次数。  
如果要取消这种行为，可以在DevTools 设置中启用 Show timestamps

### 控制台历史记录
#### 删除
* 在控制台中键入 clear()。
* 从您的 JavaScript 代码内调用 console.clear()。
* Ctrl+L

#### 保留
启用控制台顶部的 Preserve log 复选框可以在页面刷新或更改之间保留控制台历史记录

#### 保存
右键，然后选择 Save as


### 选择执行环境
默认环境设置为 top（页面的顶部框架）
