---
title: Chrome devtools-Sources面板
categories:
  - notes
tags:
  - note
  - Chrome
  - devtools
toc: true
date: 2017-12-28 10:31:20
---
chrome大概是最常用的浏览器之一，这篇笔记讲解下Chrome devtools中Sources面板的具体用法。  

[官方教程](https://developers.google.com/web/tools/chrome-devtools/)

<!-- more -->


## Sources面板
在源代码面板中设置断点来调试 JavaScript ，或者通过Workspaces（工作区）连接本地文件来使用开发者工具的实时编辑器。

### 断点类型
* 单行断点：On an exact region of code.
* 带条件的单行断点：	On an exact region of code, but only when some other condition is true.
* DOM断点：	On the code that changes or removes a specific DOM node, or its children.
* XHR断点：	When an XHR URL contains a string pattern.
* 事件监听断点：	On the code that runs after an event, such as click, is fired.
* 异常监听断点：	On the line of code that is throwing a caught or uncaught exception.
* 函数断点：	Whenever a specific function is called.

### 单行断点
#### devTools中添加
1. 打开Source面板
2. 打开要调试的文件
3. 找到要添加端点的行
4. 在左侧行号处单击（蓝色标签）

#### 程序中添加
```
console.log('b');
debugger;
console.log('c');
```


### 带条件的单行断点
1. 打开Source面板
2. 打开要调试的文件
3. 找到要添加端点的行
4. 在左侧行号处右击
5. 选择Add conditional breakpoint.
6. 输入表达式
7. 按Enter激活（橘黄标签）


### 管理单行断点
Breakpoints窗格中可以查看、激活、删除某个或多个断点

### DOM断点
DOM Breakpoints窗格中查看Elements面板上设定的DOM的Break on操作（子树更改,属性更改,节点移除）

### XHR断点
XHR Breakpoints窗格中添加。

### 事件监听断点
Event Listener Breakpoints窗格添加侦听的断点动作

### 异常监听断点
窗格中的暂停号（∥）：Pause on exceptions

### 函数断点
执行到函数时会停住
```
function sum(a, b) {
  let result = a + b; // DevTools pauses on this line.
  return result;
}
debug(sum); // Pass the function object, not a string.
sum();
```
