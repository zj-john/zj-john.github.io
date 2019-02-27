---
title: 一次JS代码的Code Golf
categories:
  - notes
tags:
  - JavaScript Weekly
  - code golf
  - JS
  - 2018.11.30
toc: true
date: 2018-12-03 13:05:02
---
[Code Golf](https://en.wikipedia.org/wiki/Code_golf)的目标是尽可能地减小程序的大小。这里是一个简单的示例。

通过本文的5个主要阶段缩减代码，您可以简单了解下在Code Golf中用到的主要技术。

<!-- more -->

## 出发

以下代码是使用画布绘制一个黑色方块，并使其从左向右移动。我们将从这块代码入手进行缩减

![](/images/JS_Weekly/code-golf-javascript/v1.gif)

```js
    // Retrieve the canvas and its context
    let canvas = document.getElementById('c');
    let ctx = canvas.getContext('2d');

    // Set the canvas size
    canvas.width = 100;
    canvas.height = 100;

    // Variable to store the x position of the square
    let x = 0;

    // Main function that draws the square and moves it
    loop();

    function loop() {
      // Clear the canvas, so we can draw on it later
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      // Change the position of the square
      if (x > canvas.width) {
        // If it's gone too far, move it to the far left
        x = -20;
      } else {
        // Otherwise, move it slightly to the right
        x = x + 2;
      }

      // Draw the black square at the position x
      ctx.fillRect(x, 20, 20, 20);

      // Call the loop function about 60 times per second
      requestAnimationFrame(loop);
    }
```
现在，除去注释，一共有344个字符。你觉得最终能缩减到多少呢？

## 第1阶段：基础知识

让我们从一些基础部分开始缩短代码：

* 关键字`let`在本代码中不是必需的，因此我们将其删除。
* 减少所有变量名称的长度，例如`canvas`变为`c`。
* 在`loop()`函数中，我们用值`100`替换`c.width`以及`c.height`。
* 用三元运算符代替`if-else`。

为了保持本文中的代码可读，删除空格，分号和空行将在最后操作。

通过这些更改，新代码如下。
```js
    c = document.getElementById('c');
    t = c.getContext('2d');
    c.width = 100;
    c.height = 100;
    x = 0;

    loop();

    function loop() {
      t.clearRect(0, 0, 100, 100);
      x = (x > 100) ? -20 : x + 2;
      t.fillRect(x, 20, 20, 20);
      requestAnimationFrame(loop);
    }
```
字符数：从344到247。

## 第2阶段：DOM和Canvas技巧

经过一些搜索后，我发现了3个与DOM和Canvas相关的技巧，这些技巧会使代码显著缩短。

第一个是关于`getElementById()`。事实证明根本没有必要使用这个函数，因为Javascript会自动为每个带有id的HTML元素创建变量。因此，要访问具有`id="c"`的画布，我们可以直接使用该变量`c`。

第二个技巧是`requestAnimationFrame()`。这是一个很好的功能，每秒刷新画布约60次，但输入也很长。所以，我们可以使用`setInterval()`。它们工作原理基本相同，但后者要短得多。

最后一招是关于`clearRect()`。有一种方法不用`clearRect()`也可以清除画布，就是通过设置画布的大小。因此，如果我们把`c.width = 100`直接放在`loop()`函数内部，画布将在每一次循环时被清除。

这是具有3个技巧的新代码。

```js
    t = c.getContext('2d');
    x = 0;

    setInterval(loop, 20);

    function loop() {
      c.width = 100;
      c.height = 100;
      x = (x > 100) ? -20 : x + 2;
      t.fillRect(x, 20, 20, 20);
    }
```
让我们用匿名函数替换`loop()`。
```js
    t = c.getContext('2d');
    x = 0;

    setInterval(() => {
      c.width = 100;
      c.height = 100;
      x = (x > 100) ? -20 : x + 2;
      t.fillRect(x, 20, 20, 20);
    }, 20);
```
字符数量：从247到154。

## 第3阶段：新的变量

如果我们以更智能的方式使用变量，可以节省一些字符。以下是一些想法：

* 第一次`setInterval()`调用时，它返回值`1`（不一定）。我们可以利用这个来直接初始化变量，比如`x`用`x = setInterval(...)`。
* 可以一次分配多个变量。例如：`c.width = c.height = 100`。
* 变量`t` （画布的上下文）仅在我们使用`t.fillRect(...)`时使用过一次。这意味着我们不需要这个变量，而应该直接写`c.getContext('2d').fillRect(...)`。

```js
    x = setInterval(() => {
      c.width = c.height = 100;
      x = (x > 100) ? -20 : x + 2;
      c.getContext('2d').fillRect(x, 20, 20, 20);
    }, 20);
```
我们还应该做两件事：

* 我们在代码中使用了5次`20`。如果我们给`20`设置一个变量`k`，就可以避免这种重复。
* 同样的，`100`使用了2次，我们用`w`变量替代。

这两个更改实际上使代码稍长。但很快，当我们删除空格和分号时，这些修改将变得有价值。
```js
    k = 20;
    x = setInterval(() => {
      w = c.width = c.height = 100;
      x = (x > w) ? -k : x + 2;
      c.getContext('2d').fillRect(x, k, k, k);
    }, k);
```
字符数：从154到141。

## 第4阶段：合并行
在Javascript中分配变量时，将返回变量本身的值。例如，当 `k = 20`时,我们设置 `k`为`20`，我们也返回值`20`。我们可以使用这个事实将两行组合成一行，就像这样。
```js
    // Before
    k = 20;
    x = setInterval(/* ... */, k);

    // After
    x = setInterval(/* ... */, k = 20);
```
使用相同的想法，我们合并这两行，并删除变量`w`。

```js
    // Before
    w = c.width = c.height = 100;
    x = (x > w) ? -k : x + 2;

    // After
    x = (x > (c.width = c.height = 100)) ? -k : x + 2;
```
还有这两行。
```js
    // Before
    x = (x > (c.width = c.height = 100)) ? -k : x + 2;
    c.getContext('2d').fillRect(x, k, k, k);

    // After
    c.getContext('2d').fillRect(x = (x > (c.width = c.height = 100)) ? -k : x + 2, k, k, k);
```
现在我们的匿名函数包含一行代码。
```js
    x = setInterval(() => {
      c.getContext('2d').fillRect(x = (x > (c.width = c.height = 100)) ? -k : x + 2, k ,k, k);
    }, k = 20);
```
字符数：从141到126。

## 第五阶段：最后的技巧
如果我们仔细阅读`setIterval()`文档，我们会发现第一个参数可以是函数也可以是字符串。
```js
    x = setInterval(
      "c.getContext('2d').fillRect(x = (x > (c.width = c.height = 100)) ? -k : x + 2, k ,k, k);",
      k = 20
    );
```
这看起来很奇怪，但它正常。  
最后，我们应该：

* 删除所有空格和换行符，因为它们都是可选的。
* 删除两个不必要的分号。
* 从三元运算符中删除一对不需要的括号。
* 更改`100`为`99`，节省一个字符。

这是最终的结果：
```
    x=setInterval("c.getContext('2d').fillRect(x=x>(c.width=c.height=99)?-k:x+2,k,k,k)",k=20)
```
字符数：从126到89。

原始代码有344个字符。现在，相同的结果，它几乎要小4倍！

## HTML
到目前为止我们只讨论过Javascript，那HTML呢？

这里是HTML和Javascript的代码，只有119个字符。为了便于阅读，我添加了一些换行符。

```html
    <canvas id=c>
    <script>
      x=setInterval("c.getContext('2d').fillRect(x=x>(c.width=c.height=99)?-k:x+2,k,k,k)",k=20)
    </script>
```
如果将这个代码保存在HTML文件中并在浏览器中打开它，您将看相同的结果。

![](/images/JS_Weekly/code-golf-javascript/v1.gif)

## 结论
从344个字符到89个字符是一个迷人的旅程。很多次我以为“没有办法再缩短”，但结果我总是错的。要确信代码可以更小。  

> 这里的示例是一个为了缩减代码而做的极端，很多点在真实场景中量力使用。

## 资源
* 原文：https://www.lesscake.com/code-golf-javascript
* Code_golf：https://en.wikipedia.org/wiki/Code_golf
* GitHub：https://github.com/lesscake/code-golf-javascript
