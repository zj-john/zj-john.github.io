---
title: 理解JavaScript中的柯里化
categories:
  - notes
tags:
  - JavaScript Weekly
  - javascript
  - currying
  - 柯里化
  - functional programming
  - 2018.10.26
toc: true
date: 2018-10-30 10:52:02
---

![](https://cdn-images-1.medium.com/max/1000/1*RZ5E9_cqR-mG6yXmfEYDFQ.png)
函数式编程是一种 把函数作为参数传递（回调）和返回无副作用（修改程序的状态）函数的 编程方式，

许多语言都采用这种编程风格，其中最受欢迎的是JavaScript，Haskell，Clojure，Erlang和Scala。

而且根据它传递和返回函数的能力，引申了许多概念：

*   纯功能
*   柯里化
*   高阶函数

我们将在本文学习的一个概念是 **柯里化（Currying）**。

在本文中，我们将学习柯里化是如何工作的以及它如何在实际开发中发挥作用。

<!-- more -->

## 什么是柯里化？
柯里化是函数式编程中的一个过程，我们可以将具有多个参数的函数转换为嵌套函数序列。它返回一个新函数，并在内部使用下一个参数。

它不断返回一个新函数, 然后消耗当前参数，就像我们之前所说的那样，直到所有参数都用完为止。所有的参数通过闭包得以保留（`"alive"`），并且当返回并执行柯里化链中的最终函数时，所有参数都在执行中使用。

> Currying是将具有多个`arity`的函数转换为具有较少`arity`函数的过程 -  [Kristina Brainwave](https://medium.com/@kbrainwave)

_注意：_ 术语`arity`是指函数的参数个数。例如，

```js
function fn(a, b) {  
    //...  
}

function _fn(a, b, c) {  
    //...  
}
```
函数`fn`接受两个参数（2-arity函数）, `_fn`接受三个参数（3-arity函数）。

因此，柯里化就是将具有多个参数的函数转换为一系列只需要**一个参数**函数的过程。

我们来看一个简单的例子：
```js
function multiply(a, b, c) {  
    return a * b * c;  
}
```
此函数接受三个数字，将数字相乘并返回结果。
```js
multiply(1,2,3); // 6
```
这里展示了如何使用完整参数调用乘法函数。  
下面，让我们创建一个`柯里化`的函数版本，看看我们如何在一系列函数调用中实现相同的功能，并得到相同的结果：

```js
function multiply(a) {  
    return (b) => {  
        return (c) => {  
            return a * b * c  
        }  
    }  
}

log(multiply(1)(2)(3)) // 6
```
这里，我们已经将`multiply(1,2,3)`函数调用转为了`multiply(1)(2)(3)`这样的多个函数调用。

为了得到相乘的结果，1，2，3这三个参数会一个接一个地传递，每个数字都预填充到下一个函数内，等待调用。

我们可以将`multiply(1)(2)(3)`分开来更好地理解它：
```
const mul1 = multiply(1);  
const mul2 = mul1(2);  
const result = mul2(3);  
log(result); // 6
```
让我们一个接一个的分析。  
首先，我们传递参数`1`给`multiply`函数：
```js
let mul1 = multiply(1);
```
它返回函数：
```js
return (b) => {  
        return (c) => {  
            return a * b * c  
        }  
    }
```
现在，`mul1`转为上面的函数定义，它需要一个参数`b`。

接着，我们调用`mul1`函数，传入参数`2`：
```js
let mul2 = mul1(2);
```
在`mul1`将返回第三个函数：
```js
return (c) => {  
            return a * b * c  
        }
```
返回的函数现在存储在`mul2`变量中。
从本质上讲，`mul2`是：
```js
mul2 = (c) => {  
            return a * b * c  
        }
```
当`mul2`调用参数`3`，
```js
const result = mul2(3);
```
它与先前通过的参数：`a = 1`, `b = 2`一起计算并返回`6`。
```js
log(result); // 6
```
作为嵌套函数，`mul2`可以访问外部函数的变量范围，`multiply`以及`mul1`。

这是`mul2`可以使用前置函数中定义的变量执行乘法运算的前提。  
虽然这些函数早已返回，并且在内存中`垃圾回收`了，但它的变量仍以某种方式保持"alive"。

您会看到三个数字一次一个地应用于该函数，并且每次都返回一个新函数，直到所有数字都用完为止。

让我们看另一个例子：
```js
function volume(l,w,h) {  
    return l * w * h;  
}

const aCylinder = volume(100,20,90) // 180000l
```
我们有一个`volume`函数，它可以计算任何立方体的体积。

curried版本将接受一个参数并返回一个函数，该函数也将接受一个参数并返回一个函数。然后循环往复，直到到达最后一个参数并返回最后一个函数，然后执行所有前置参数和最后一个参数的乘法运算。
```js
function volume(l) {  
    return (w) => {  
        return (h) => {  
            return l * w * h  
        }  
    }  
}  
const aCylinder = volume(100)(20)(90) // 180000
```
与我们在`multiply`函数中所使用的一样，最后一个函数只接受参数`h`，但是将使用已包含在函数作用域的其他变量来执行最终操作。  
因为**闭包**，所以这种方式是有效的。

_currying背后的想法是获取一个函数并派生一个返回特殊函数的函数。_

## 数学中的柯里化
[维基百科](https://en.m.wikipedia.org/wiki/Currying)进一步给出了柯里化的概念。让我们用下面的例子来学习。

如果我们有一个等式：
```
    f(x,y) = x^2 + y = z
```
有两个变量x和y。如果两个变量是`x=3`和`y=4`，就可以计算`z`的值。

让我们在`f(x,y)`替换`x`和`y`的值：
```
    f(x,y) = f(3,4) = x^2 + y = 3^2 + 4 = 13 = z
```
我们得到结果，`13`。

我们可以柯里化`f(x,y)`：
```
    h = x^2 + y = f(x,y)
    hy(x) = x^2 + y = hx(y) = x^2 + y
```
**注意**：**hx** 中 **x** 是下标，**hy** 中 **y** 是下标。

如果我们在等式`hx(y) = x^2 + y`中固定`x=3`，它将返回一个以`y`作为变量的新的等式：
```
    h3(y) = 3^2 + y = 9 + y
```
**注意** ：**h3** 中 **3** 是下标

上式等同于：
```
    h3(y) = h(3)(y) = f(3,y) = 3^2 + y = 9 + y
```

最终返回了一个需要变量`y`的新方程式`9 + y`。
接下来，我们传入`y=4`：
```
    h3(4) = h(3)(4) = f(3,4) = 9 + 4 = 13
```
`y`是柯里化变量链中的最后一个变量，在前一个变量`x = 3`仍保留的前提下，执行了相加操作，最终算结果为`13`。

事实上，我们将等式`f(x,y) = 3^2 + y`柯里化为了一系列等式：
```
    3^2 + y -> 9 + y

    f(3,y) = h3(y) = 3^2 + y = 9 + y
    f(3,y) = 9 + y
    f(3,4) = h3(4) = 9 + 4 = 13
```

哇！这是数学中的柯里化，如果你发现讲解的不够清楚😕，可以阅读👉 [维基百科](https://en.m.wikipedia.org/wiki/Currying)，了解全部细节。

## 柯里化和部分函数应用（Partial function application）

现在，有些人可能会开始认为柯里化函数的嵌套函数数量取决于它接收的参数的数量。是的，这就是 **柯里化**。

我可以把 _volume_ 的柯里化函数设计为这样：
```js
function volume(l) {  
    return (w, h) => {  
        return l * w * h  
    }  
}
```
所以它可以像这样被调用：

```
const hCy = volume(70);

hCy(203,142);  
hCy(220,122);  
hCy(120,123);
```

或者

```
volume(70)(90,30);  
volume(70)(390,320);  
volume(70)(940,340);
```
我们刚刚定义了一个专门的函数来计算任何长度为（`l`）的圆柱体的体积。

它需要`3`个参数并具有`2`层嵌套函数，不像我们以前的版本，需要`3`参数时要做`3`层嵌套函数。

这个版本不是柯里化,只是 _volume_ 函数的部分应用。

柯里化和部分应用是相关的，但它们有不同的概念。

部分应用程序将函数转换为具有较小arity的另一个函数。

```js
// from
function acidityRatio(x, y, z) {  
    return performOp(x,y,z)  
}
// to
function acidityRatio(x) {  
    return (y,z) => {  
        return performOp(x,y,z)  
    }  
}
```
**注意**：我故意省略了`performOp`函数的实现，它不是重点。  
你必须要了解的是柯里化和部分应用的概念。

这是acidityRatio函数的部分应用，不涉及到柯里化的问题。  
acidityRatio函数被部分应用以接受较少的arity，最终期望参数的数量少于其原始函数。

如果把它柯里化，它会是这样的：
```js
function acidityRatio(x) {  
    return (y) = > {  
        return (z) = > {  
            return performOp(x,y,z)  
        }  
    }  
}
```
柯里化根据函数的参数个数创建嵌套函数。每个函数都接收一个参数。如果没有参数，也就没有柯里化了。

> Currying适用于具有两个以上参数的函数 - [_Wikipedia_](https://en.m.wikipedia.org/wiki/Currying)

可能存在一种情况，让柯里化和部分应用彼此相遇，如下。  
假设我们有一个函数：
```js
function div(x,y) {  
    return x/y;  
}
```
如果我们部分应用它。我们将得到：
```js
function div(x) {  
    return (y) => {  
        return x/y;  
    }  
}
```
同时，柯里化将给我们相同的结果：
```js
function div(x) {  
    return (y) => {  
        return x/y;  
    }  
}
```
虽然柯里化和部分函数有可能给出相同的结果，但它们仍是两个不同的概念。

就像我们之前说的那样，柯里化和部分应用是相关的，但实际上并没有相同的设计。他们之间的共同点是他们都依靠闭包来工作。

## 柯里化有用吗？

当然，当您遇到以下场景时，柯里化会派上用场：

1. **编写可以轻松重用和配置的小代码模块，就像我们使用npm一样：**
例如，您拥有一家商店🏠并希望为您的客户提供10％💵的折扣：
```js
function discount(price, discount) {  
    return price * discount  
}
```
当一个客户购买价值500美元的商品时，你会给他：
```js
const price = discount(500,0.10); // $50   
// $500  - $50 = $450
```
从长远来看，我们会发现自己每天都在计算10％的折扣。
```js
const price = discount(1500,0.10); // $150  
// $1,500 - $150 = $1,350

const price = discount(2000,0.10); // $200  
// $2,000 - $200 = $1,800

const price = discount(50,0.10); // $5  
// $50 - $5 = $45

const price = discount(5000,0.10); // $500  
// $5,000 - $500 = $4,500

const price = discount(300,0.10); // $30  
// $300 - $30 = $270
```
我们可以把discount功能柯里化，这样我们就不用总是添加0.10折扣：
```js
function discount(discount) {  
    return (price) => {  
        return price * discount;  
    }  
}

const tenPercentDiscount = discount(0.1);
```
现在，我们现在只需要您客户购买的商品价格就可以计算了：
```js
tenPercentDiscount(500); // $50  
// $500 - $50 = $450
```
同样，有些优惠客户比其它优惠客户更重要，让我们称之为超级客户。我们希望为超级客户提供20％的折扣。

我们使用柯里化：
```js
const twentyPercentDiscount = discount(0.2);
```
我们通过使用0.2折扣值创建了一个新的柯里化函数，
返回的函数`twentyPercentDiscount`将用于计算我们超级客户的折扣：
```js
twentyPercentDiscount(500); // 100  
// $500 - $100 = $400  
twentyPercentDiscount(5000); // 1000  
// $5,000 - $1,000 = $4,000  
twentyPercentDiscount(1000000); // 200000  
// $1,000,000 - $200,000 = $600,000
```
2. **避免频繁调用具有相同参数的函数**：
例如，我们有一个计算圆柱体积的函数：
```js
function volume(l, w, h) {  
    return l * w * h;  
}
```
碰巧仓库中的所有圆柱高度都为100米。你会看到，你会反复使用`h`为`100`调用此函数：
```js
volume(200,30,100) // 2003000l  
volume(32,45,100); //144000l  
volume(2322,232,100) // 53870400l
```
要解决这个问题，你可以调整`volume`函数（就像我们之前做的那样）：
```js
function volume(h) {  
    return (w) => {  
        return (l) => {  
            return l * w * h  
        }  
    }  
}
```
我们可以为特定的圆柱高度定义一个特定的函数：
```js
const hCylinderHeight = volume(100);

hCylinderHeight(200)(30); // 600,000l  
hCylinderHeight(2322)(232); // 53,870,400l
```

## 通用柯里化函数
让我们开发一个函数，它接受任何函数并返回函数的柯里化版本。

> 此函数不是唯一的，你可以写自己的版本

```js
function curring(fn){
  var _args = [];
  return function cb(){
    if(arguments.length === 0) {
      return fn.apply(this, _args);
    }
    Array.prototype.push.apply(_args, [].slice.call(arguments));
    return cb;
  }
}
```

示例如下：
```js
function volume(l,h,w) {  
    return l * h * w  
}

console.log(curring(volume)(100)(200)(900)())
// 18000000

const hCy = curring(volume)(100);

hCy(200)(900)(); // 18000000l  
hCy(70)(60)(); // 420000l

```

## 通用部分函数

```js
function PartialFunction(fn, ...args) {  
    return (..._arg) => {  
        return fn(...args, ..._arg);  
    }  
}
```
示例如下：

```js
function volume(l,h,w) {  
    return l * h * w  
}

const hCy = PartialFunction(volume,100);

hCy(200,900); // 18000000l  
hCy(70,60); // 420000l
```

## 结论
闭包使得可以在JavaScript中进行柯里化。它能够保留已经执行的函数的状态，使我们能够创建一个函数为特定值的工厂函数。

柯里化，闭包和函数式编程是比较棘手的一块儿内容。但通过不断的练习，一旦掌握它，你会知道付出是值得的。

## 资源
* 原文 https://blog.bitsrc.io/understanding-currying-in-javascript-ceb2188c339
* Currying — Wikipedia https://en.m.wikipedia.org/wiki/Currying
* Partial Application Function — Wikipedia https://en.m.wikipedia.org/wiki/Partial_application
