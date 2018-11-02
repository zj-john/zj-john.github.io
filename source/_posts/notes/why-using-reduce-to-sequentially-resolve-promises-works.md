---
title: 为什么使用reduce来解决顺序Promises问题
categories:
  - notes
tags:
  - JavaScript Weekly
  - browsers
  - performance
  - javascript
  - 2018.10.19
toc: true
date: 2018-10-23 11:33:00
---
不使用`Promise`对象的来编写异步JavaScript的情况非常类似于闭着眼睛烘烤蛋糕。虽然能做到，但它会变得混乱，你可能会把自己玩坏。

我可没说 _必须_ 用`Promise`，但你理解我的意思。它真的很棒。但有时候，它需要一些帮助来解决一些独特的挑战，**例如当你试图按顺序，解决一串promises时**。这样的例子很常见，例如，当您需要对一批AJAX返回的数据排序时，您希望服务器处理这些事情，但不能同时处理，而是按照时间顺序一件件处理。

如果不使用有助于简化此任务的软件包（如Caolan McMahon的[异步库](https://github.com/caolan/async)），那么解决顺序promise问题的最普遍的方案是使用`Array.prototype.reduce()`。你可能听说过[它](https://css-tricks.com/understanding-the-almighty-reducer/)。它接收一组参数，对参数中的每个元素应用函数，最终将其简化为单个值并返回，如下所示：

```js
    let result = [1,2,5].reduce((accumulator, item) => {
      return accumulator + item;
    }, 0); // <-- Our initial value.

    console.log(result); // 8
```
不过，对于上面提过的顺序promise问题使用`reduce()`，代码看起来是这样的：
```js
    let userIDs = [1,2,3];

    userIDs.reduce( (previousPromise, nextID) => {
      return previousPromise.then(() => {
        return methodThatReturnsAPromise(nextID);
      });
    }, Promise.resolve());
```
或者，以更新的格式：
```js
    let userIDs = [1,2,3];

    userIDs.reduce( async (previousPromise, nextID) => {
      await previousPromise;
      return methodThatReturnsAPromise(nextID);
    }, Promise.resolve());
```
这很整洁！但是在最长的时间里，我只是囫囵使用这个解决方案并将代码复制到我的应用程序中，并没有去深入理解。  
所以，在这篇文章中，我来通过探索两件事情来深入理解：

1.  为什么可行？
2.  为什么我们不能用其他`Array`方法做同样的事情呢？

<!-- more -->

## 为什么可行
请记住，`reduce()`的主要目的是将一堆东西“减少”为一个东西，这是通过在循环运行中将结果存储在`accumulator`来实现的。但这`accumulator`不一定是数字。循环可以返回它想要的任何内容（如promise），并在每次迭代时通过回调把该值循环使用。值得注意的是，无论`accumulator`的值是什么，循环本身的行为都不会改变（包括其执行速度）。它会在线程允许的情况下持续整个行为。

这可能很难理解，因为它可能违背了你对循环中发生的事情的认知。**当我们使用`reduce()`来解决顺序promises时，循环实际上并没有减慢**。它是完全同步的，尽可能快地完成，就像往常一样。

请看以下代码段，并注意循环进度根本不会受回调中返回的promise的阻碍。
```js
    function methodThatReturnsAPromise(nextID) {
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          console.log(`Resolve! ${dayjs().format('hh:mm:ss')}`);
          resolve();
        }, 1000);
      });
    }

    [1,2,3].reduce( (accumulatorPromise, nextID) => {
      console.log(`Loop! ${dayjs().format('hh:mm:ss')}`);

      return accumulatorPromise.then(() => {
        return methodThatReturnsAPromise(nextID);
      });
    }, Promise.resolve());
```
在我们的控制台：
```shell
    "Loop! 11:28:06"
    "Loop! 11:28:06"
    "Loop! 11:28:06"
    "Resolve! 11:28:07"
    "Resolve! 11:28:08"
    "Resolve! 11:28:09"
```
promises按照我们的预期顺序执行，而且循环本身是快速，稳定和同步的。  
建议看下MDN中为`reduce()`做的[polyfill](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce#Polyfill),这样能帮助你理解。
下面是polyfill中的部分后台逻辑代码，可以看出`while()`一遍又一遍循环触发`callback()`，整个过程没有异步：
```js
    while (k < len) {
      if (k in o) {
        value = callback(value, o[k], k, o);
      }
      k++;
    }
```
考虑到以上因素，这个例子中真正有意思的事情发生在这里：
```js
    return previousPromise.then(() => {
      return methodThatReturnsAPromise(nextID)
    });
```
每当我们的回调触发时，我们都会返回一个promise做为 _另一个_ promise的resolve。虽然`reduce()`不触发任何实际逻辑函数，但它确实提供了能够在每次运行后将某些内容传递回同一个回调的功能，这是`reduce()`的一项独特功能。  
因此，我们能够构建一系列promise，然后resolve进更多的promise，使一切变得美观和有秩序：
```js
    new Promise( (resolve, reject) => {
      // Promise #1

      resolve();
    }).then( (result) => {
      // Promise #2

      return result;
    }).then( (result) => {
      // Promise #3

      return result;
    }); // ... and so on!
```
所有这也揭示了为什么我们不能每次迭代都返回 _一个新的promise_。因为循环是同步运行的，所以每个promise都会立即触发，而不是等待它之前创建的那些。
```js
    [1,2,3].reduce( (previousPromise, nextID) => {

      console.log(`Loop! ${dayjs().format('hh:mm:ss')}`);

      return new Promise((resolve, reject) => {
        setTimeout(() => {
          console.log(`Resolve! ${dayjs().format('hh:mm:ss')}`);
          resolve(nextID);
        }, 1000);
      });
    }, Promise.resolve());
```
在我们的控制台：
```shell
    "Loop! 11:31:20"
    "Loop! 11:31:20"
    "Loop! 11:31:20"
    "Resolve! 11:31:21"
    "Resolve! 11:31:21"
    "Resolve! 11:31:21"
```
是否可以等到所有前置处理完成后再做某项事情？答案是肯定的。
`reduce()`的同步性并不意味着在完成所有项目的处理后你不能继续做点其他事。看下面代码：
```js
    function methodThatReturnsAPromise(id) {
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          console.log(`Processing ${id}`);
          resolve(id);
        }, 1000);
      });
    }

    let result = [1,2,3].reduce( (accumulatorPromise, nextID) => {
      return accumulatorPromise.then(() => {
        return methodThatReturnsAPromise(nextID);
      });
    }, Promise.resolve());

    result.then(e => {
      console.log("Resolution is complete! Let's party.")
    });
```
因为我们在回调函数中返回的所有内容都是一个链式promise，所以我们在循环完成时得到的全部内容只是一个promise。在循环完成之后，我们可以按照我们想要的方式后续处理它。


## 为什么我们不能用其他`Array`方法做同样的事情呢？
请记住，在`reduce()`中，进入下一次迭代之前，我们不会等待回调完成。它是完全同步的。所有其他这些方法都是如此：

*   [`Array.prototype.map()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map#Polyfill)
*   [`Array.prototype.forEach()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach#Polyfill)
*   [`Array.prototype.filter()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter#Polyfill)
*   [`Array.prototype.some()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some#Polyfill)
*   [`Array.prototype.every()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/every#Polyfill)

**但`reduce()`很特别**。

我们发现，`reduce()`对我们有用的原因是因为我们能够在相同的回调中将某些东西返回（即promise），然后我们可以通过将其resolve到另一个promise的方式来构建。  
但是，使用所有其他方法，我们无法将从回调函数返回的参数再传递给下一个回调。相反，每个回调参数都是预先确定的，这是我们无法利用它们来解决顺序promise问题的原因。
```js
    [1,2,3].map((item, [index, array]) => [value]);
    [1,2,3].filter((item, [index, array]) => [boolean]);
    [1,2,3].some((item, [index, array]) => [boolean]);
    [1,2,3].every((item, [index, array]) => [boolean]);
```
## 资源
* 原文 https://tylermcginnis.com/ultimate-guide-to-execution-contexts-hoisting-scopes-and-closures-in-javascript/
* Reduce https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce
