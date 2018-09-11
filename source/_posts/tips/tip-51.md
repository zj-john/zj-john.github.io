---
title: Failed to execute removeChild on Node（React）
categories:
  - tips
tags:
  - React
  -
toc: false
date: 2018-05-22 15:32:14
---

开发时遇到报错：React: Failed to execute removeChild on Node。

如下，更改isActive时会报错：
```js
//App.js

render() {
    {
      this.state.isActive?
        <Test />:null
    }
}

// Test.js

render() {
  <canvas />
}
```

<!-- more -->

解决方案：  
更改为如下
```js
// Test.js

render() {
  <div>
    <canvas />
  </div>
}
```
