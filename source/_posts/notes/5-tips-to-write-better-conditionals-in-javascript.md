---
title: 5个技巧，让你在JS中编写更好的条件语句
categories:
  - if
  - javascript
  - 2018.10.19
tags:
  - JavaScript Weekly
toc: true
date: 2018-10-22 14:34:58
---

![](/images/JS_Weekly/5-tips-to-write-better-conditionals-in-javascript/udpahiv8rqlemvz0x3wc.png)

<!-- more -->
当使用JavaScript时，我们总会遇到许多条件语句。本文中，有针对条件语句的5个技巧，可以让你的代码更清晰。

## 对多条件的判断语句使用Array.includes

我们来看下面的例子：
```js
    // condition
    function test(fruit) {
      if (fruit == 'apple' || fruit == 'strawberry') {
        console.log('red');
      }
    }
```
乍一看，上面的代码还不错。但是，当我们的水果集中有更多的红色水果，比如`cherry`和`cranberries`？我们要使用更多的`||`来扩展声明吗？

事实上，我们可以使用`[(Array.includes)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/includes)`来重写上面的条件语句。
```js
    function test(fruit) {
      // extract conditions to array
      const redFruits = ['apple', 'strawberry', 'cherry', 'cranberries'];

      if (redFruits.includes(fruit)) {
        console.log('red');
      }
    }
```
我们将`red fruits`（判断条件）提取到数组中，通过`Array.includes`来使用它。这样写，代码看起来更整洁。

## 少嵌套，早返回

让我们扩展下前面的示例，给它增加2个额外的条件：
* 如果水果参数为空，抛出错误
* 接收水果数量参数，如果数量超过10，则打印。

```js
    function test(fruit, quantity) {
      const redFruits = ['apple', 'strawberry', 'cherry', 'cranberries'];

      // condition 1: fruit must has value
      if (fruit) {
        // condition 2: must be red
        if (redFruits.includes(fruit)) {
          console.log('red');

          // condition 3: must be big quantity
          if (quantity > 10) {
            console.log('big quantity');
          }
        }
      } else {
        throw new Error('No fruit!');
      }
    }

    // test results
    test(null); // error: No fruits
    test('apple'); // print: red
    test('apple', 20); // print: red, big quantity
```
在上面的代码中，我们有：

* 1个过滤无效条件的if/else语句
* 1个3级嵌套的if语句（条件1,2和3）

在处理此类问题时，我个人遵循的一般规则是 **在发现无效条件时提前返回** 。

```js
    /_ return early when invalid conditions found _/

    function test(fruit, quantity) {
      const redFruits = ['apple', 'strawberry', 'cherry', 'cranberries'];

      // condition 1: throw error early
      if (!fruit) throw new Error('No fruit!');

      // condition 2: must be red
      if (redFruits.includes(fruit)) {
        console.log('red');

        // condition 3: must be big quantity
        if (quantity > 10) {
          console.log('big quantity');
        }
      }
    }
```
这样做可以使我们减少一个级别的嵌套。而且这种编码风格很好，特别是当你有很长的if语句时，你无需滚动到最底层才知道有一个else语句。

通过反转判断条件并提前返回，我们可以进一步减少嵌套。请查看下面的第2个条件语句，看看我们是如何做到的：
```js
    /_ return early when invalid conditions found _/

    function test(fruit, quantity) {
      const redFruits = ['apple', 'strawberry', 'cherry', 'cranberries'];

      if (!fruit) throw new Error('No fruit!'); // condition 1: throw error early
      if (!redFruits.includes(fruit)) return; // condition 2: stop when fruit is not red

      console.log('red');

      // condition 3: must be big quantity
      if (quantity > 10) {
        console.log('big quantity');
      }
    }
```
通过反转第2个条件语句的条件，我们的代码不再嵌套语句。特别是，当我们代码逻辑很长时，及时返回非常有用。

但是，这并不是一个硬性标准，你要自己来判断优劣。

对我来说，我更喜欢带有嵌套的条件语句。这是因为：

* 代码逻辑简短直接，嵌套if更清晰
* 反转条件可能会引发更多的思考过程（增加认知负荷）

因此，**旨在减少嵌套和尽早返回，但不要过度**。如果您感兴趣，下面的一篇文章和StackOverflow中的讨论会进一步讨论：

*   [Avoid Else, Return Early](http://blog.timoxley.com/post/47041269194/avoid-else-return-early) by Tim Oxley
*   [StackOverflow discussion](https://softwareengineering.stackexchange.com/questions/18454/should-i-return-from-a-function-early-or-use-an-if-statement) on if/else coding style

## 使用默认的函数参数和解构

我想下面的代码可能看起来很熟悉，在JS中我们总是需要检查`null` / `undefined`值和分配默认值：

```js
    function test(fruit, quantity) {
      if (!fruit) return;
      const q = quantity || 1; // if quantity not provided, default to one

      console.log(`We have ${q} ${fruit}!`);
    }

    //test results
    test('banana'); // We have 1 banana!
    test('apple', 2); // We have 2 apple!
```
实际上，我们可以通过给函数分配默认参数来消除变量`q`。
```js
    function test(fruit, quantity = 1) { // if quantity not provided, default to one
      if (!fruit) return;
      console.log(`We have ${quantity} ${fruit}!`);
    }

    //test results
    test('banana'); // We have 1 banana!
    test('apple', 2); // We have 2 apple!
```
更简单直观不是吗？请注意，每个参数都可以指定自己的[函数默认参数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Default_parameters)。例如，我们也可以指定`fruit`的默认值：`function test(fruit = 'unknown', quantity = 1)`。

如果`fruit`是一个对象怎么办？我们可以指定默认参数吗？

```js
    function test(fruit) {
      // printing fruit name if value provided
      if (fruit && fruit.name)  {
        console.log (fruit.name);
      } else {
        console.log('unknown');
      }
    }

    //test results
    test(undefined); // unknown
    test({ }); // unknown
    test({ name: 'apple', color: 'red' }); // apple
```
看看上面的例子，如果水果的名称属性可用，则打印，否则打印`unknown`。我们可以使用使用函数默认参数和解构来替代`fruit && fruit.name`的条件检查。

```js
    // destructing - get name property only
    // assign default empty object {}
    function test({name} = {}) {
      console.log (name || 'unknown');
    }

    //test results
    test(undefined); // unknown
    test({ }); // unknown
    test({ name: 'apple', color: 'red' }); // apple
```
因为我们只需要`name`属性，所以可以使用解构参数`{name}`，然后我们可以使用变量`name`在代码中代替`fruit.name`。

我们还将空对象`{}`指定为默认值。如果不这样做，你在执行时会报错`test(undefined)` \- `Cannot destructure property name of 'undefined' or 'null'.`.因为undefined中没有`name`属性。

如果您不介意使用第三方库，有几种方法可以减少空检查：

* 使用Lodash get函数
* 使用Facebook开源的[idx](https://github.com/facebookincubator/idx)（与Babeljs结合使用）

以下是使用Lodash的示例：

```js
    // Include lodash library, you will get _
    function test(fruit) {
      console.log(_.get(fruit, 'name', 'unknown')); // get property name, if not available, assign default value 'unknown'
    }

    //test results
    test(undefined); // unknown
    test({ }); // unknown
    test({ name: 'apple', color: 'red' }); // apple
```

您可以在[此处](http://jsbin.com/bopovajiye/edit?js,console)运行演示代码。此外，如果您是函数式编程（FP）的粉丝，您可以选择使用[Lodash fp](https://github.com/lodash/lodash/wiki/FP-Guide)，Lodash的函数式版本（方法为`get`或`getOr`）。

## 选择Map/Object而不是Switch语句
让我们看看下面的例子，我们想根据颜色打印水果：
```js
    function test(color) {
      // use switch case to find fruits in color
      switch (color) {
        case 'red':
          return ['apple', 'strawberry'];
        case 'yellow':
          return ['banana', 'pineapple'];
        case 'purple':
          return ['grape', 'plum'];
        default:
          return [];
      }
    }

    //test results
    test(null); // []
    test('yellow'); // ['banana', 'pineapple']
```
上面的代码没有语法错误，但我觉得它很冗长。使用对象字面量可以实现相同的结果：
```js
    // use object literal to find fruits in color
      const fruitColor = {
        red: ['apple', 'strawberry'],
        yellow: ['banana', 'pineapple'],
        purple: ['grape', 'plum']
      };

    function test(color) {
      return fruitColor[color] || [];
    }
```
您还可以使用[Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)来实现相同的结果：
```js
    // use Map to find fruits in color
      const fruitColor = new Map()
        .set('red', ['apple', 'strawberry'])
        .set('yellow', ['banana', 'pineapple'])
        .set('purple', ['grape', 'plum']);

    function test(color) {
      return fruitColor.get(color) || [];
    }
```
[Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) 是ES2015标准中可用的对象类型，允许您存储键值对。

_我们应该禁止使用switch语句吗_？就我个人而言，我尽可能使用对象字面量，但我不会设置硬规则来阻止它。你需要根据具体情形选择。

Todd Motto有一篇文章深入研究switch语句与对象文字，你可以在[这里][here](https://toddmotto.com/deprecating-the-switch-statement-for-object-literals/)阅读。


### 重构
对于上面的例子，我们实际上可以使用`Array.filter`来重构我们的代码。
```js
     const fruits = [
        { name: 'apple', color: 'red' },
        { name: 'strawberry', color: 'red' },
        { name: 'banana', color: 'yellow' },
        { name: 'pineapple', color: 'yellow' },
        { name: 'grape', color: 'purple' },
        { name: 'plum', color: 'purple' }
    ];

    function test(color) {
      // use Array filter to find fruits in color

      return fruits.filter(f => f.color == color);
    }
```
总有不止一种方法可以达到相同的效果，这就是编码的乐趣！

## 使用Array.every和Array.some
最后一个提示是利用新的（但也没那么新的）Javascript Array函数来减少代码行。看下面的代码，我们想检查是否所有水果都是红色的：
```js
    const fruits = [
        { name: 'apple', color: 'red' },
        { name: 'banana', color: 'yellow' },
        { name: 'grape', color: 'purple' }
      ];

    function test() {
      let isAllRed = true;

      // condition: all fruits must be red
      for (let f of fruits) {
        if (!isAllRed) break;
        isAllRed = (f.color == 'red');
      }

      console.log(isAllRed); // false
    }
```
代码太长了！我们可以通过`Array.every`减少行数：
```js
    const fruits = [
        { name: 'apple', color: 'red' },
        { name: 'banana', color: 'yellow' },
        { name: 'grape', color: 'purple' }
      ];

    function test() {
      // condition: short way, all fruits must be red
      const isAllRed = fruits.every(f => f.color == 'red');

      console.log(isAllRed); // false
    }
```
现在精简多了。
以类似的方式，如果我们想测试水果中是否有红色，我们可以用`Array.some`来实现它。
```js
    const fruits = [
        { name: 'apple', color: 'red' },
        { name: 'banana', color: 'yellow' },
        { name: 'grape', color: 'purple' }
    ];

    function test() {
      // condition: if any fruit is red
      const isAnyRed = fruits.some(f => f.color == 'red');

      console.log(isAnyRed); // true
    }
```

## 资源
* 原文：https://scotch.io/bar-talk/5-tips-to-write-better-conditionals-in-javascript
