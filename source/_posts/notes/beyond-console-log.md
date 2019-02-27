---
title: 超越console.log
categories:
  - notes
tags:
  - JavaScript Weekly
  - javascript
  - console
  - 2018.11.09
toc: true
date: 2018-11-13 10:52:02
---

调试JavaScript不止是用console.log输出一些值。这话听起来，感觉我要推荐一些调试器了，但实际上不会。

使用浏览器的单步调试功能很酷，而且你肯定会在某个时间用到。不过，大部分时间，你只是想知道代码的某一部分是否执行或者变量是什么，而不用深入到某个代码库的核心部分。

尽管如此，虽然`console.log`有用，但很多人并没有意识到它`console`下有很多超越`log`的选择。适当使用这些功能可以使调试更容易，更快速，更直观。

<!-- more -->

## console.log
虽然大多数人都以`console.log(object)`这种方式使用它，你也可以这样做`console.log(object, otherObject, string)`，它会将它们都整齐地记录下来。

更重要的是，还有另一种格式：`console.log(msg, values)`。这非常类似于C或PHP中的`sprintf`方法。

```js
console.log('I like %s but I do not like %s.', 'Skittles', 'pus');
```
将完全按照您的预期输出。
```
> I like Skittles but I do not like pus.
```
常见的占位符是`%o`（字母o，而不是零）代替对象，`%s`代替字符串，`%d`代替一个十进制或整数。

![](/images/JS_Weekly/beyond-console-log/1_k36EIUqbxmWeYwZVqOrzNQ.png)

另一个有趣的是`%c`，它的情况有所不同，因为它实际上是CSS值的占位符。
```js
console.log('I am a %cbutton', 'color: white; background-color: orange; padding: 2px 5px; border-radius: 2px');
```
![](/images/JS_Weekly/beyond-console-log/1_LetSPI-9ubOuADejUa_YSA.png)

这些样式会用到其后面的任何内容上，而且可以不用“结束标记”，这有点奇怪。  

当然也可以使用。
![](/images/JS_Weekly/beyond-console-log/1_cHWO5DRw9c2z9Jv_Fx2AvQ.png)

它不优雅，也不是特别有用。当然，这不是一个真正的按钮。

![](/images/JS_Weekly/beyond-console-log/1_0qgPtZGOZKBKPi1Va5wf2A.png)

## console.dir
在大多数情况下，虽然`console.dir()`看起来和`log()`有点不同，但实际功能非常类似。

![](/images/JS_Weekly/beyond-console-log/1_AUEqpGMNKtp28OK057V3Ow.png)

按下小箭头将显示与`console.log`相同的对象细节。

不过，当你输出内容是元素时，这种分歧更加剧烈，更有趣。

```js
let element = document.getElementById('2x-container');
```
这是`log`的输出：

![](/images/JS_Weekly/beyond-console-log/1_l7ujPmSWwpH7QtXCZ-jk2Q.png)

这里我们可以清楚地浏览DOM及其元素。  

但是`console.dir(element)`的输出完全不同。

![](/images/JS_Weekly/beyond-console-log/1_CERwy7Fs7tdijOxugLW54A.png)
它以更加客观的方式来查看元素。

## console.warn
它可能是`log()`最直接的替代品，您可以以完全相同的方式使用`console.warn()`。  
唯一真正的区别是输出带了黄色。具体来说，是输出了处于警告而不是信息级别的信息，因此浏览器将稍微区别对待它，这使其在杂乱输出中带有更明显的效果。

但是，有一个更大的优势。由于输出是警告而不是信息，因此您可以过滤掉所有内容`console.log`并仅保留`console.warn`。这对于偶尔会在浏览器中输出大量无用内容的应用程序尤其有用。

## console.table
`console.table()`功能旨在以一种更整洁的显示表格数据的方式展示原始对象数组。

例如，这是一个数据列表。
```js
const transactions = [{  
  id: "7cb1-e041b126-f3b8",  
  seller: "WAL0412",  
  buyer: "WAL3023",  
  price: 203450,  
  time: 1539688433  
},  
{  
  id: "1d4c-31f8f14b-1571",  
  seller: "WAL0452",  
  buyer: "WAL3023",  
  price: 348299,  
  time: 1539688433  
},  
{  
  id: "b12c-b3adf58f-809f",  
  seller: "WAL0012",  
  buyer: "WAL2025",  
  price: 59240,  
  time: 1539688433  
}];
```
如果我们使用`console.log` ，我们会得到如下不太有用的信息：
```
▶ (3) \[{…}, {…}, {…}\]
```
点击小箭头可以让你打开具体信息，当然，但这并不是我们想要的“一目了然”。

这时，`console.table(data)`更有帮助。

![](/images/JS_Weekly/beyond-console-log/1_wr2e5dAr_K5ilwMsYMetgw.png)

第二个参数是可选的，表示您想要展示的列。显然默认为所有列，但我们也可以这样做。
```js
> console.table(data, ["id", "price"]);
```
我们得到了只显示id和价格的输出。这适用于过大的，只需要展示一些细节的对象。索引列是自动创建的，并且据我所知不会消失。

![](/images/JS_Weekly/beyond-console-log/1__je_I8pwxVgFjvCnwybMDw.png)

这里注意，列是可以排序的。

另外，`console.table()` 只能处理最多1000行，因此可能不适合所有数据集。

## console.assert
另一个很有用的函数是`assert()`，它和`log()`的功能类似，但仅在第一个参数为假的情况下。如果第一个参数为真，它什么都不做。【断言】  

这对于有循环（或几个不同的函数调用），但只有一个特定行为需要显示的情况特别有用。  
基本上它等同于下面的代码。
```js
if (object.whatever === 'value') {  
  console.log(object);  
}
```
澄清一下，这里说的“相同”，实际上是相反。因为你需要反转条件，为false时才输出。

让我们假设时间戳中有一个值是`null`或`0`。这要识别出来，不然会搞砸我们的日期格式化。
```js
console.assert(tx.timestamp, tx);
```

当任何有效的数据传过来时，它只会跳过去。但是当`0`或`null`传入时会触发我们的日志记录，因为对应的值是false。

有时我们想要更复杂的条件。例如，我们已经看到了用户`WAL0412`数据的问题，所以只想显示来自它们的交易。这是直观的解决方案。

```js
console.assert(tx.buyer === 'WAL0412', tx);
```
这看起来正确，但不起作用。请记住，条件必须是假的...我们要 _断言_，而不是 _过滤_ 。
```js
console.assert(tx.buyer !== 'WAL0412', tx);
```
这才是我们想要的。

## console.count
count是一个计数器，可选择作为一个命名计数器。
```js
for(let i = 0; i < 10000; i++) {
  if(i % 2) {
    console.count('odds');
  }
  if(!(i % 5)) {
    console.count('multiplesOfFive');
  }
  if(isPrime(i)) {
    console.count('prime');
  }
}
```
这段代码有点抽象，没有实际用处。此外，我不打算演示这个`isPrime`函数，我们假装它有效。  
我们得到的是如下列表：
```
odds: 1  
odds: 2  
prime: 1  
odds: 3  
multiplesOfFive: 1  
prime: 2  
odds: 4  
prime: 3  
odds: 5  
multiplesOfFive: 2  
...
```
这对于您只是转储索引，或者您希望保留一个（或多个）运行计数的情况很有用。
您也可以直接使用`console.count()`，无需参数。这样做默认叫做`default`。
如果您愿意，还可以使用`console.resetCount()`重置计数器。

### console.trace
这个函数很难用简单的数据进行演示。因为它擅长的是当你在类或库中试图找出一个真正导致问题的调用者时。

例如，可能有12个不同的组件调用服务，但其中一个组件没有正确设置依赖关系。
```js
export default class CupcakeService {

  constructor(dataLib) {
    this.dataLib = dataLib;
    if(typeof dataLib !== 'object') {
      console.log(dataLib);
      console.trace();
    }
  }
  ...
}
```
`console.log()`这里将告诉我们传递的dataLib的内容是什么，而不是在哪里。但是，堆栈跟踪将非常清楚地告诉我们是`new CupcakeService(false)`导致的错误，在`Dashboard.js`这个文件下。

### console.time
用于跟踪操作所用时间的专用函数console.time()，它是跟踪JavaScript执行所用时间的更好方法。
```js
function slowFunction(number) {
  var functionTimerStart = new Date().getTime();
  // something slow or complex with the numbers.
  // Factorials, or whatever.
  var functionTime = new Date().getTime() - functionTimerStart;
  console.log(`Function time: ${ functionTime }`);
}
var start = new Date().getTime();

for (i = 0; i < 100000; ++i) {
  slowFunction(i);
}

var time = new Date().getTime() - start;
console.log(`Execution time: ${ time }`);
```
这是一种老式的方法。

让我们看看新的。

```js
const slowFunction = number =>  {
  console.time('slowFunction');
  // something slow or complex with the numbers.
  // Factorials, or whatever.
  console.timeEnd('slowFunction');
}
console.time();

for (i = 0; i < 100000; ++i) {
  slowFunction(i);
}
console.timeEnd();
```
我们现在不再需要做任何数学运算或设置临时变量就能得到想要的时间了。

### console.group
现在我们可能学习的是控制台输出中最复杂和最高级的部分。group让你......好吧，分组。特别是它可以让你嵌套输出。它擅长显示代码中常用的结构。

```js
// this is the global scope
let number = 1;
console.group('OutsideLoop');
console.log(number);
console.group('Loop');
for (let i = 0; i < 5; i++) {
  number = i + number;
  console.log(number);
}
console.groupEnd();
console.log(number);
console.groupEnd();
console.log('All done now');
```
这比较粗糙，但你可以看到输出。

![](/images/JS_Weekly/beyond-console-log/1_4Dil0L35FnGxiVPJx4mJsQ.png)

虽然不是很有用，但你可能会看到其中一些是如何组合在一起的。

```js
class MyClass {
  constructor(dataAccess) {
    console.group('Constructor');
    console.log('Constructor executed');
    console.assert(typeof dataAccess === 'object',
      'Potentially incorrect dataAccess object');
    this.initializeEvents();
    console.groupEnd();
  }
  initializeEvents() {
    console.group('events');
    console.log('Initialising events');
    console.groupEnd();
  }
}
let myClass = new MyClass(false);
```
![](/images/JS_Weekly/beyond-console-log/1_MW0eKpxlBK-Cf9atJv3Baw.png)
上面有很多工作和用于调试信息的代码，这可能不是那么有用。但它仍然是一个有趣的想法，你可以看到它是如何更清晰使的展示上下文的日志记录。

最后一个内容，即`console.groupCollapsed`。它在功能上和`console.group`是相同的，区别是一个默认展开，一个默认折叠。

## 结论
这里没有太多结论。如果您可能只需要比`console.log(pet)`多一点点，而不是调试器，所有这些方法都可能有用，

可能最有用的是`console.table`，但其它函数也有各自的适用场景。我喜欢用`console.assert`，因为可以在特定条件下调试的东西。

## 资源
* 原文：https://medium.com/@mattburgess/beyond-console-log-2400fdf4a9d8
