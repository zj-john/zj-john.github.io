---
title: 自ES2015以来，JavaScript对象中的属性顺序是可预测的
categories:
  - notes
tags:
  - JavaScript Weekly
  - javascript
  - object
  - property order
  - 2018.10.26
toc: true
date: 2018-10-29 10:42:25
---

下面这段React的代码，最终渲染的效果依赖于对象中属性的顺序。
```js
    styles({
      backgroundBlue: true,
      backgroundRed: true
    });
```
但从执行效果来看，背景将显示为红色，因为它是“放在最后的属性”。  
如果你已经用了一段时间的JavaScript，你可能会立刻想到 - 等等，什么！？属性是有顺序的吗？

<!-- more -->

## 常见的误解 - “JavaScript中无法保证属性的顺序”

几年前写JavaScript时，您可能听到过JS对象中的属性顺序是不可预测的说法。  
虽然我还没遇到过一些对顺序有强制要求的需求，但我始终遵循“从不依赖属性顺序”的规则。

### 内部方法`ownPropertyKeys`
实际上，从ES2015以来，就已经有一些包含属性顺序规则的方法了，这得益于内部方法`ownPropertyKeys`的实现。  

对象中属性的顺序取决于所包含属性的类型及其值。具体的顺序规则可以查看定义在“[内部方法ownPropertyKeys](http://www.ecma-international.org/ecma-262/6.0/#sec-ordinary-object-internal-methods-and-internal-slots-ownpropertykeys)”中的准则。有些新的方法已经在使用这种规则，例如`Object.getOwnPropertyNames`和`Reflect.ownKeys`。

规则如下：
```
[[OwnPropertyKeys]] ( )
When the [[OwnPropertyKeys]] internal method of O is called the following steps are taken:

1. Let keys be a new empty List.
2. For each own property key P of O that is an integer index, in ascending numeric index order
   a. Add P as the last element of keys.
3. For each own property key P of O that is a String but is not an integer index, in property creation order
   a. Add P as the last element of keys.
4. For each own property key P of O that is a Symbol, in property creation order
   a. Add P as the last element of keys.
5.Return keys.
```

有趣的是，一些方法的规格在变化，例如`Object.keys`从[ES5](https://www.ecma-international.org/ecma-262/5.1/#sec-15.2.3.14)到[ES6](http://www.ecma-international.org/ecma-262/6.0/#sec-object.keys)的变化。ES6规范定义了`Object.keys`也依赖于`ownPropertyKeys`，这使得属性顺序在支持ES6的浏览器中可预测。

但这也意味着您必须小心使用此方法，并且不应该强依赖于它来对属性顺序的预测，因为`Object.keys`可能会因[浏览器实现](https://www.reddit.com/r/javascript/comments/9ozyn3/property_order_is_predictable_in_javascript/e7yhm3g/)的差异导致结果不同。

原理讲的差不多了，现在让我们来看看代码。


#### 整数
以整数索引为key的所有属性，首先出现在整个对象属性顺序中，并之间以数字方式排序。
```js
    const objWithIndices = {
      23: 23,
      '1': 1,
      1000: 1000
    };

    console.log(Reflect.ownKeys(objWithIndices));
    // [1, 23, 1000]
    // ☝️ following numeric order
```

>'1'是整数索引，而'01'不是。你可以这样理解，对一个数组 const a=[1,2,3], a['0']===a[0],值是1，但a['01']值是undefined。

#### 字符串（不是整数）
key不计入整数索引且不是`Symbol`类型的属性，是第二顺序，且它们间按照时间顺序排列。
```js
    const objWithStrings = {
      'bar': 'bar',
      '01': '01'
    };

    objWithStrings.last = 'last';
    objWithStrings['veryLast'] = 'veryLast';

    console.log(Reflect.ownKeys(objWithStrings));
    // ['bar', '01', 'last', 'veryLast']
    // ☝️ following chronological order
```
#### Symbols
最后是Symbols，之间也遵循时间顺序。
```js
    const objWithSymbols = {
      [Symbol('first')]: 'first',
      [Symbol('second')]: 'second'
    };

    objWithSymbols[Symbol('last')] = 'last';

    console.log(Reflect.ownKeys(objWithSymbols));
    // [Symbol(first), Symbol(second), Symbol(last)]
    // ☝️ following chronological order
```
#### 组合规则
-------------------------------
组合这些规则时，您将看到整数始终位于对象属性的“前排”，后跟字符串和Symbols。**此外，我们可以控制字符串和Symbols属性的顺序，因为它们是按时间顺序排列的！**
```js
    const obj = {
      '2': 'integer: 2',
      'foo': 'string: foo',
      '01': 'string: 01',
      1: 'integer: 1',
      [Symbol('first')]: 'symbol: first'
    };

    obj['0'] = '0';
    obj[Symbol('last')] = 'symbol: last';
    obj['veryLast'] = 'string: very last';

    console.log(Reflect.ownKeys(obj));
    // [ "0", "1", "2", "foo", "01", "veryLast", Symbol(first), Symbol(last) ]
    // -> 1. integers in numeric order
    // -> 2. strings in chronological order
    // -> 3. Symbols in chronological order
```
_但是，正如这篇[文章](https://twitter.com/malgosiastp/status/1052539495453773824)指出的，“自有属性顺序”规则仅在一些现代浏览器中得到完全支持，而其他则不行，比如IE不支持_。

## 资源
* 原文: https://www.stefanjudis.com/today-i-learned/property-order-is-predictable-in-javascript-objects-since-es2015/
*   [The traversal order of object properties in ES6](http://2ality.com/2015/10/property-traversal-order-es6.html#traversing-the-own-keys-of-an-object)
*   [ECMAScript® 2019 Language Specification - OrdinaryOwnPropertyKeys](https://tc39.github.io/ecma262/#sec-ordinaryownpropertykeys)
*   ["Does ES6 introduce a well-defined order of enumeration for object properties?"](https://stackoverflow.com/questions/30076219/does-es6-introduce-a-well-defined-order-of-enumeration-for-object-properties) on Stack Overflow
