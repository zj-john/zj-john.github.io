---
title: object deep copy
categories:
  - tips
tags:
  - object
  - deep copy
toc: true
date: 2018-02-05 15:27:04
---
> 问题： object deep copy

一般来说，object变量的copy和引用都是基于内存地址的浅拷贝，不注意的话很容易出现问题。此时深拷贝就很必要。  
本文列举下深拷贝的几种方法：

<!-- more -->

## 解决方案

### 原生
```js
JSON.parse(JSON.stringify(obj))
```
> 此种方法有个问题，如果object中某个key对应的值是函数或者undefined，则转换后会丢失该key。


### JQuery
```js
// Shallow copy
var newObject = jQuery.extend({}, oldObject);

// Deep copy
var newObject = jQuery.extend(true, {}, oldObject);
```

### Lodash
[cloneDeep](https://lodash.com/docs#cloneDeep)
```js
var objects = [{ 'a': 1 }, { 'b': 2 }];

var deep = _.cloneDeep(objects);
console.log(deep[0] === objects[0]);
// => false
```
