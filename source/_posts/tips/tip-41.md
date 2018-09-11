---
title: setState Cannot update during an existing state transition
categories:
  - tips
tags:
  - react
toc: false
date: 2018-02-24 10:38:54
---
> 问题：setState Cannot update during an existing state transition

<!-- more -->
React中，render时是不能进行props或者state的更改的，否则就会报上述错误。  
原因是React中，props或state的变化是可以监听到并重新render的，那如果render中，props或者state再更改，就产生了一个render死循环。

产生这个问题的原因，除了render中显示更改props或者state的值之外，更多的可能是不小心触发了更改。  
最常见的场景如下：

```
<Button onClick={doSomeAction('param')}>Test</Button>

doSomeAction (param) => {
  this.setState({
    ...
  });
}

```
这种写法看起来没什么问题，但是会触发上述报错。看下正确的写法：
```
<Button onClick={() => doSomeAction('param')}>Test</Button>
```

错误的写法在render时，直接执行了doSomeAction这个函数，触发了state的改变；正确的写法把它封装了一层，表示这是一个等待触发的函数。

但是以下方式是可以的：
```
<Button onClick={doSomeAction()}>Test</Button>
```
即不加参数的函数时可以直接写的。
