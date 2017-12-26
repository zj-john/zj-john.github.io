---
title: node中export一个异步函数
categories:
  - tips
tags:
  - node
  - export
  - asynchronous
toc: true
date: 2017-12-14 12:07:45
---
> 问题：node中export一个异步函数

开发中遇到需要把一个异步函数写到common中，然后export出来的情况。  
方法如下：

<!-- more -->

## single callback
```javascript
// module.js

// foo:需要经过异步函数处理后的值
// callback: 得到异步处理后的foo值后 需要执行的操作
var foo, callback;
someAsyncFunction(function(response) {
    // 执行到这里，说明异步函数已经完成
    foo = "the value after async";
    // 执行进if说明 上次来调用时 异步还未执行完成
    if( typeof callback == 'function' ){
        callback(foo);
    }
});

// cb是外部传入的callback
module.exports = function(cb){
    if(typeof foo != 'undefined'){
        cb(foo);
    } else {
        // 当前异步还未执行完成，把cb储存在callback中
        callback = cb;
    }
}
```


```javascript
// main.js
var fooMod = require('./foo.js');
fooMod(function(foo){
    //Here code using foo;
});
```

## mutiple callback
多个callback函数，非链式。

```javascript
// module.js
var foo, callbackList = [];
someAsyncFunction(function(response) {
    foo = "the value after async";

    // 循环调用当前的积累的callback函数
    for(var i = 0; i < callbackList.length; i++){
        callbackList[i](foo)
    }
});

module.exports = function(cb){
    if(typeof foo != 'undefined'){
        cb(foo);
    } else {
        callback.push(cb);
    }
}
```


```javascript
// main.js
var fooMod = require('./foo.js');
fooMod(function(foo){
    //Here code using foo;
});
```




## promise
ES6最优方式

```javascript
// module.js
const asyncFunc = () => {
    return new Promise((resolve, reject) => {
        // Where someAsyncFunction takes a callback, i.e. api call
        someAsyncFunction(data => {
            resolve(data)
        })
    })
}

export default asyncFunc
```


```javascript
// main.js
import asyncFunc from './asyncFunc'
asyncFunc().then(data => { console.log(data) })
```
