---
title: JavaScript原型入门指南
categories:
  - notes
  - JavaScript
  - prototype
  - 2018.10.05
tags:
  - JavaScript Weekly
toc: true
date: 2018-10-08 14:13:35
---

![A Beginner's Guide to JavaScript's Prototype image](/images/JS_Weekly/A Beginner's Guide to JavaScript's Prototype/beginnersGuideToJavaScriptPrototype-e39c1309a86e48a3bff4d7b6eb4a9fe7-b64db.png)

如果不能很好的处理对象，你将无法在JavaScript中获得很大的成就。它们几乎是JavaScript编程语言各个方面的基础。在这篇文章中，您将了解用于实例化新对象的各种模式，在学习中，您将逐渐深入了解JavaScript的原型。

<!-- more -->

### 基础

如果不能很好的处理对象，你将无法在JavaScript中获得很大的成就。它们几乎是JavaScript编程语言各个方面的基础。事实上，学习如何创建对象可能是你刚开始学习的第一件事。话虽如此，为了最有效地学习JavaScript中的原型，我们将从基础开始。

首先，对象是键/值对。创建对象的最常用方法是使用花括号`{}`，并使用点表示法向对象添加属性和方法。

```js
    let animal = {}
    animal.name = 'Leo'
    animal.energy = 10

    animal.eat = function (amount) {
      console.log(`${this.name} is eating.`)
      this.energy += amount
    }

    animal.sleep = function (length) {
      console.log(`${this.name} is sleeping.`)
      this.energy += length
    }

    animal.play = function (length) {
      console.log(`${this.name} is playing.`)
      this.energy -= length
    }
```

这很简单。现在，我们需要创建多个动物。所以将上面的逻辑封装在函数中，以便我们随时调用。我们将此模式称为`函数实例化` ，将函数本身称为“构造函数”，因为它负责“构造”一个​​新对象。

#### 函数实例化

```js
    function Animal (name, energy) {
      let animal = {}
      animal.name = name
      animal.energy = energy

      animal.eat = function (amount) {
        console.log(`${this.name} is eating.`)
        this.energy += amount
      }

      animal.sleep = function (length) {
        console.log(`${this.name} is sleeping.`)
        this.energy += length
      }

      animal.play = function (length) {
        console.log(`${this.name} is playing.`)
        this.energy -= length
      }

      return animal
    }

    const leo = Animal('Leo', 7)
    const snoop = Animal('Snoop', 10)
```
现在，每当我们想要创建一个新动物（或者更广泛地说是一个新的“实例”）时，我们所要做的就是调用我们的`Animal`函数，然后把 `name`和`energy`参数传递进去。这非常有效，而且非常简单。  
但是，你能发现这种模式的缺点吗？最大也是我们会尝试解决的一个和三种方法 `eat`，`sleep`和`play`有关。这些方法中的每一种都不仅是动态的，而且它们也是完全通用的。这意味着没有理由重新创建这些方法，正如我们在创建新动物时所做的那样。我们只是在浪费内存，让每个动物物体都比它需要的更大。你能想到一个解决方案吗？如果不是每次创建新动物时重新创建这些方法，我们将它们移动到自己的对象然后我们可以让每个动物引用该对象，该怎么办？我们可以将这种模式称为 `使用共享方法的函数实例化`。

#### 使用共享方法的函数实例化
```js
    const animalMethods = {
      eat(amount) {
        console.log(`${this.name} is eating.`)
        this.energy += amount
      },
      sleep(length) {
        console.log(`${this.name} is sleeping.`)
        this.energy += length
      },
      play(length) {
        console.log(`${this.name} is playing.`)
        this.energy -= length
      }
    }

    function Animal (name, energy) {
      let animal = {}
      animal.name = name
      animal.energy = energy
      animal.eat = animalMethods.eat
      animal.sleep = animalMethods.sleep
      animal.play = animalMethods.play

      return animal
    }

    const leo = Animal('Leo', 7)
    const snoop = Animal('Snoop', 10)
```
通过将共享方法移动到它们自己的对象并在我们的`Animal` 函数内部引用该对象，我们现在已经解决了内存浪费和过大的动物对象的问题。


#### Object.create

让我们使用`Object.create`再次改进我们的例子。简而言之，**Object.create允许您创建一个对象，该对象将在失败的查找中委托给另一个对象**。换句话说，Object.create允许您创建一个对象，只要该对象上的属性查找失败，它就可以查询另一个对象以查看该另一个对象是否具有该属性。我们来看一些代码。

```js
    const parent = {
      name: 'Stacey',
      age: 35,
      heritage: 'Irish'
    }

    const child = Object.create(parent)
    child.name = 'Ryan'
    child.age = 7

    console.log(child.name) // Ryan
    console.log(child.age) // 7
    console.log(child.heritage) // Irish
```

因此，在上面的示例中，因为`child`是通过`Object.create(parent)`创建的，只要在`child`上有失败的属性查找，JavaScript就会将该查找委托给该`parent`对象。这意味着即使`child`没有`heritage`属性，当你查找`child.heritage`时你会得到它`parent`的heritage属性`Irish`。

现在，通过使用`Object.create`，我们如何来简化我们之前的`Animal`代码呢？好吧，我们可以使用Object.create来继承`animalMethods`对象，而不是像现在一样逐一向动物添加所有共享方法。这听起来很智能，让我们称之为`使用共享方法和Object.create进行函数实例化`


#### 使用共享方法和Object.create进行函数实例化
```js
    const animalMethods = {
      eat(amount) {
        console.log(`${this.name} is eating.`)
        this.energy += amount
      },
      sleep(length) {
        console.log(`${this.name} is sleeping.`)
        this.energy += length
      },
      play(length) {
        console.log(`${this.name} is playing.`)
        this.energy -= length
      }
    }

    function Animal (name, energy) {
      let animal = Object.create(animalMethods)
      animal.name = name
      animal.energy = energy

      return animal
    }

    const leo = Animal('Leo', 7)
    const snoop = Animal('Snoop', 10)

    leo.eat(10)
    snoop.play(5)
```
所以现在当我们调用时`leo.eat`，JavaScript将eat在leo对象上查找方法。那个查找将失败，因为Object.create，它将委托给`animalMethods`对象，然后它将找到`eat`。

到现在为止还挺好。尽管如此，我们仍然可以做出一些改进。为了跨实例共享方法，必须管理一个单独的对象（`animalMethods`）似乎有点“骇客” 。但这似乎是您希望在语言本身中实现的常见功能。其实它一直都在，这也是你在这里的原因 - `原型`。

那究竟什么是JavaScript中的`原型`呢？好吧，简单地说，JavaScript中的每个函数都有一个`prototype`引用对象的属性。亲自测试一下。

```js
    function doThing () {}
    console.log(doThing.prototype) // {}
```
如果不是创建一个单独的对象来管理我们的方法（就像我们正在做的那样`animalMethods`），我们只是将每个方法放在`Animal`函数的原型上呢？然后，我们所要做的就是不使用Object.create代理`animalMethods`，我们可以使用它来委托`Animal.prototype`。我们称之为这种模式`原型实例化`。


#### 原型实例化
```js
    function Animal (name, energy) {
      let animal = Object.create(Animal.prototype)
      animal.name = name
      animal.energy = energy

      return animal
    }

    Animal.prototype.eat = function (amount) {
      console.log(`${this.name} is eating.`)
      this.energy += amount
    }

    Animal.prototype.sleep = function (length) {
      console.log(`${this.name} is sleeping.`)
      this.energy += length
    }

    Animal.prototype.play = function (length) {
      console.log(`${this.name} is playing.`)
      this.energy -= length
    }

    const leo = Animal('Leo', 7)
    const snoop = Animal('Snoop', 10)

    leo.eat(10)
    snoop.play(5)
```
希望你有一个很大的“啊哈”时刻。同样，`prototype`它只是JavaScript中每个函数都具有的属性，并且如上所述，它允许我们在函数的所有实例之间共享方法。我们所有的功能仍然是相同的，但现在我们不必为所有方法管理一个单独的对象，我们可以使用内置于`Animal`函数本身的另一个对象，`Animal.prototype`。


* * *

让我们更深入
==================

到目前为止，我们知道三件事：

1. 如何创建构造函数。
2. 如何将方法添加到构造函数的原型中。
3. 如何使用Object.create将失败的查找委托给函数的原型。
这三个任务似乎是任何编程语言的基础。JavaScript是否真的那么糟糕，没有更简单，“内置”的方式来完成同样的事情？正如你可能在这一点上猜测的那样，它是通过使用`new`关键字来实现的。

我们采取的缓慢，有条理的方法有什么好处，你现在可以深入了解`new`关键字在JavaScript中的底层做了什么。

回顾一下我们的`Animal` 构造函数，最重要的两个部分是创建对象和返回它。如果不创建对象`Object.create`，我们将无法在失败的查找上委托函数的原型。如果没有该`return`语句，我们将永远不会收回创建的对象。

```js
    function Animal (name, energy) {
      let animal = Object.create(Animal.prototype)
      animal.name = name
      animal.energy = energy

      return animal
    }
```
这是很酷的事情`new` \- 当你使用`new`关键字调用一个函数时，这两行是隐式地完成的，并且调用创建的对象`this`。

使用注释来显示在幕后发生的事情并假设`Animal`使用`new`关键字调用构造函数，可以将其重写为此。

```js
    function Animal (name, energy) {
      // const this = Object.create(Animal.prototype)

      this.name = name
      this.energy = energy

      // return this
    }

    const leo = new Animal('Leo', 7)
    const snoop = new Animal('Snoop', 10)
```
没有注释的情况
```js
    function Animal (name, energy) {
      this.name = name
      this.energy = energy
    }

    Animal.prototype.eat = function (amount) {
      console.log(`${this.name} is eating.`)
      this.energy += amount
    }

    Animal.prototype.sleep = function (length) {
      console.log(`${this.name} is sleeping.`)
      this.energy += length
    }

    Animal.prototype.play = function (length) {
      console.log(`${this.name} is playing.`)
      this.energy -= length
    }

    const leo = new Animal('Leo', 7)
    const snoop = new Animal('Snoop', 10)
```
这个满足要求以及`this`为指向我们的创建对象的原因是因为我们使用`new`关键字调用了构造函数。如果在调用函数时不使用`new`，则this这个对象永远不会创建，也不会隐式返回。我们可以在下面的示例中看到这个问题。
```js
    function Animal (name, energy) {
      this.name = name
      this.energy = energy
    }

    const leo = Animal('Leo', 7)
    console.log(leo) // undefined

```
这种模式的名称是`伪初始化`。

如果JavaScript不是您的第一种编程语言，您可能会有点不安。

> “WTF，这个家伙只是重新创造了一个更糟糕的版本” - 你

对于那些不熟悉的人，使用Class（类）允许您为对象创建模板。然后，无论何时创建该类的实例，都会获得一个具有模板中定义的属性和方法的对象。

听起来有点熟？这基本上就是我们对上面`Animal`的构造函数所做的。我们只是使用常规的旧JavaScript函数重新创建相同的功能，而不是使用关键字`class`。当然，它需要一些额外的工作以及一些关于JavaScript“后台”发生的事情的知识，但结果是一样的。

这里有个好消息。JavaScript不是一种死语言。它不断得到改进，并由[TC-39委员会](https://tylermcginnis.com/videos/ecmascript/)补充。这意味着即使JavaScript的初始版本不支持类，也不影响后续将它们添加到官方规范中。事实上，这正是TC-39委员会所做的。2015年，发布了EcmaScript（官方JavaScript规范）6，支持Classes和`class`关键字。让我们看看`Animal`上面的构造函数如何使用新的类语法。

```js
    class Animal {
      constructor(name, energy) {
        this.name = name
        this.energy = energy
      }
      eat(amount) {
        console.log(`${this.name} is eating.`)
        this.energy += amount
      }
      sleep(length) {
        console.log(`${this.name} is sleeping.`)
        this.energy += length
      }
      play(length) {
        console.log(`${this.name} is playing.`)
        this.energy -= length
      }
    }

    const leo = new Animal('Leo', 7)
    const snoop = new Animal('Snoop', 10)
```
很干净吧？

因此，如果这是创建类的新方法，为什么我们花了这么多时间来翻过旧的方式呢？之所以这样，是因为新的方式（使用`class`关键字）主要只是我们称之为伪古典模式的现有方式的“语法糖”。为了完全理解ES6类的便捷语法，首先必须理解伪古典模式。


* * *
在这一点上，我们已经介绍了JavaScript原型的基础知识。本文的其余部分将致力于理解与其相关的其他“幸好知道了这件事”主题。在另一篇文章中，我们将看看如何利用这些基础知识并使用它们来理解继承在JavaScript中的工作原理。

* * *

### 数组方法
我们在上面深入讨论了如果要在类的实例之间共享方法，您应该将这些方法放在类（或函数）原型上。如果我们了解Array类型，我们可以看到同样的模式。从历史上看，您可能已经创建了这样的数组
```js
    const friends = []
```
事实证明，这是通过`new`创造一个`Array`类实例的语法糖。

```js
    const friendsWithSugar = []

    const friendsWithoutSugar = new Array()
```
你可能从来没有想过一件事是怎样一个array的每个实例都具有所有这些内置的方法（`splice`，`slice`，`pop`等）？

正如您现在所知，这是因为这些方法存在`Array.prototype`，当您创建新实例时`Array`，您使用`new`关键字将`Array.prototype`失败的查找设置为委托。

我们可以通过查看`Array.prototype`简单的查看所有数组的方法。

```js
    console.log(Array.prototype)

    /*
      concat: ƒn concat()
      constructor: ƒn Array()
      copyWithin: ƒn copyWithin()
      entries: ƒn entries()
      every: ƒn every()
      fill: ƒn fill()
      filter: ƒn filter()
      find: ƒn find()
      findIndex: ƒn findIndex()
      forEach: ƒn forEach()
      includes: ƒn includes()
      indexOf: ƒn indexOf()
      join: ƒn join()
      keys: ƒn keys()
      lastIndexOf: ƒn lastIndexOf()
      length: 0n
      map: ƒn map()
      pop: ƒn pop()
      push: ƒn push()
      reduce: ƒn reduce()
      reduceRight: ƒn reduceRight()
      reverse: ƒn reverse()
      shift: ƒn shift()
      slice: ƒn slice()
      some: ƒn some()
      sort: ƒn sort()
      splice: ƒn splice()
      toLocaleString: ƒn toLocaleString()
      toString: ƒn toString()
      unshift: ƒn unshift()
      values: ƒn values()
    */
```
对象也存在完全相同的逻辑。所有的对象将失败的查找委托给Object.prototype，这就是为什么所有对象都有类似`toString`和`hasOwnProperty`的方法。


### 静态方法
到目前为止，我们已经介绍了为什么以及如何在类的实例之间共享方法。但是，如果我们有一个对Class很重要但不需要跨实例共享的方法呢？例如，如果我们有一个函数接受一个`Animal`实例数组并确定下一个要被吃的的需求该怎么办？我们称之为`nextToEat`。

```js
    function nextToEat (animals) {
      const sortedByLeastEnergy = animals.sort((a,b) => {
        return a.energy - b.energy
      })

      return sortedByLeastEnergy[0].name
    }
```
这是没有意义有`nextToEat`依托于`Animal.prototype`，因为我们不希望所有实例之间共享。相反，我们可以将其视为辅助方法。所以，如果`nextToEat`不应该依托于`Animal.prototype`，我们应该把它放在哪里？那么显而易见的答案是我们可以把`nextToEat`与我们`Animal`类放到相同的范围，然后在我们正常需要时引用它。

```js
    class Animal {
      constructor(name, energy) {
        this.name = name
        this.energy = energy
      }
      eat(amount) {
        console.log(`${this.name} is eating.`)
        this.energy += amount
      }
      sleep(length) {
        console.log(`${this.name} is sleeping.`)
        this.energy += length
      }
      play(length) {
        console.log(`${this.name} is playing.`)
        this.energy -= length
      }
    }

    function nextToEat (animals) {
      const sortedByLeastEnergy = animals.sort((a,b) => {
        return a.energy - b.energy
      })

      return sortedByLeastEnergy[0].name
    }

    const leo = new Animal('Leo', 7)
    const snoop = new Animal('Snoop', 10)

    console.log(nextToEat([leo, snoop])) // Leo
```
现在这可行，但有更好的方法。
> 要有一个特定于类本身的方法，但不需要在该类的实例之间共享，就可以将其添加为static类的属性。

```js
    class Animal {
      constructor(name, energy) {
        this.name = name
        this.energy = energy
      }
      eat(amount) {
        console.log(`${this.name} is eating.`)
        this.energy += amount
      }
      sleep(length) {
        console.log(`${this.name} is sleeping.`)
        this.energy += length
      }
      play(length) {
        console.log(`${this.name} is playing.`)
        this.energy -= length
      }
      static nextToEat(animals) {
        const sortedByLeastEnergy = animals.sort((a,b) => {
          return a.energy - b.energy
        })

        return sortedByLeastEnergy[0].name
      }
    }
```
现在，因为我们在类上添加`nextToEat`了一个`static`属性，它就存在于`Animal`类本身（而不是它的原型）上，可以使用它来访问`Animal.nextToEat`。

```js
    const leo = new Animal('Leo', 7)
    const snoop = new Animal('Snoop', 10)

    console.log(Animal.nextToEat([leo, snoop])) // Leo

```
因为我们在这篇文章中都遵循了类似的模式，让我们来看看如何使用ES5完成同样的事情。在上面的例子中，我们看到了如何使用static关键字将方法直接放在类本身上。使用ES5，同样的模式就像手动将方法添加到函数对象一样简单。

```js
    function Animal (name, energy) {
      this.name = name
      this.energy = energy
    }

    Animal.prototype.eat = function (amount) {
      console.log(`${this.name} is eating.`)
      this.energy += amount
    }

    Animal.prototype.sleep = function (length) {
      console.log(`${this.name} is sleeping.`)
      this.energy += length
    }

    Animal.prototype.play = function (length) {
      console.log(`${this.name} is playing.`)
      this.energy -= length
    }

    Animal.nextToEat = function (nextToEat) {
      const sortedByLeastEnergy = animals.sort((a,b) => {
        return a.energy - b.energy
      })

      return sortedByLeastEnergy[0].name
    }

    const leo = new Animal('Leo', 7)
    const snoop = new Animal('Snoop', 10)

    console.log(Animal.nextToEat([leo, snoop])) // Leo
```
### 获取对象的原型
无论您使用哪种模式创建对象，都可以使用该`Object.getPrototypeOf`方法完成获取该对象的原型。
```js
    function Animal (name, energy) {
      this.name = name
      this.energy = energy
    }

    Animal.prototype.eat = function (amount) {
      console.log(`${this.name} is eating.`)
      this.energy += amount
    }

    Animal.prototype.sleep = function (length) {
      console.log(`${this.name} is sleeping.`)
      this.energy += length
    }

    Animal.prototype.play = function (length) {
      console.log(`${this.name} is playing.`)
      this.energy -= length
    }

    const leo = new Animal('Leo', 7)
    const prototype = Object.getPrototypeOf(leo)

    console.log(prototype)
    // {constructor: ƒ, eat: ƒ, sleep: ƒ, play: ƒ}

    prototype === Animal.prototype // true
```
上面的代码有两个重要的要点。

首先，你会发现，`proto`是以个含有4种方法的对象，`constructor`，`eat`，`sleep`，和`play`。这就说得通了。我们使用`getPrototypeOf`方法进入到实例，`leo`返回实例的原型，这是我们所有方法都存在的地方。这告诉我们还有一件事`prototype`我们还没有谈过。默认情况下，该`prototype`对象将具有一个`constructor`属性，该属性指向原始函数或创建实例的类。这也意味着因为JavaScript默认情况下会在属性上放置一个`constructor`属性，所以任何实例都可以通过`instance.constructor`它访问它们的构造函数。

从上面得到的第二个重要内容是`Object.getPrototypeOf(leo) === Animal.prototype`。这也是有道理的。该`Animal`构造函数有一个原型属性，在那里我们可以分享的所有实例的方法和`getPrototypeOf`让我们看到了实例本身的原型。

```js
    function Animal (name, energy) {
      this.name = name
      this.energy = energy
    }

    const leo = new Animal('Leo', 7)
    console.log(leo.constructor) // Logs the constructor function
```
为了配合我们之前讨论的内容`Object.create`，其工作原因是因为任何实例`Animal`都将委托给`Animal.prototype`失败的查找。因此，当您尝试访问时`leo.prototype`，`leo`没有`prototype`属性，因此它会将该查找委托给`Animal.prototype`确实具有`constructor`属性的查找。如果这一段不太了解，请回过头来阅读`Object.create`上述内容。


> 您可能已经看过__proto__之前用于获取实例的原型。这是过去的遗物。相反，如上所述使用**Object.getPrototypeOf(instance)**。

### 确定属性是否存在于原型上
在某些情况下，您需要知道属性是否存在于实例本身上，还是存在于对象委托的原型上。我们可以通过循环遍历`leo`我们一直在创建的对象来看到这一点。假设目标是循环`leo`并记录其所有键和值。使用for in循环，可能看起来像这样。

```js
    function Animal (name, energy) {
      this.name = name
      this.energy = energy
    }

    Animal.prototype.eat = function (amount) {
      console.log(`${this.name} is eating.`)
      this.energy += amount
    }

    Animal.prototype.sleep = function (length) {
      console.log(`${this.name} is sleeping.`)
      this.energy += length
    }

    Animal.prototype.play = function (length) {
      console.log(`${this.name} is playing.`)
      this.energy -= length
    }

    const leo = new Animal('Leo', 7)

    for(let key in leo) {
      console.log(`Key: ${key}. Value: ${leo[key]}`)
    }
```
你期望看到什么？最有可能的是，它是这样的
```
    Key: name. Value: Leo
    Key: energy. Value: 7
```
但是，如果你运行代码，你看到的是这个 -

```
    Key: name. Value: Leo
    Key: energy. Value: 7
    Key: eat. Value: function (amount) {
      console.log(`${this.name} is eating.`)
      this.energy += amount
    }
    Key: sleep. Value: function (length) {
      console.log(`${this.name} is sleeping.`)
      this.energy += length
    }
    Key: play. Value: function (length) {
      console.log(`${this.name} is playing.`)
      this.energy -= length
    }
```
...
这是为什么？一个`for in`循环将循环遍历对象本身以及它所委托的原型的所有**可枚举属性**。因为默认情况下将添加到函数的原型任何属性可枚举，我们不仅看到了`name`和`energy`，但我们也看到所有的原型方法- `eat`，`sleep`和`play`。要解决这个问题，我们需要指定所有原型方法都是不可枚举的，或者如果属性位于leo对象本身上，我们需要一种只有console.log的方法，而不是leo在失败的查找中委托给的原型。这是`hasOwnProperty`可以帮助我们的地方。

`hasOwnProperty`是每个对象上的属性，它返回一个布尔值，指示对象是否具有指定的属性作为其自己的属性，而不是对象委托给的原型。这正是我们所需要的。现在有了这些新知识，我们可以修改我们的代码以利用循环`hasOwnProperty`内部的优势`for in`。

```js
    ...

    const leo = new Animal('Leo', 7)

    for(let key in leo) {
      if (leo.hasOwnProperty(key)) {
        console.log(`Key: ${key}. Value: ${leo[key]}`)
      }
    }
```
现在我们看到的只是leo对象本身的属性，而不是原型leo委托。
```
    Key: name. Value: Leo
    Key: energy. Value: 7
```
如果你仍然有点困惑hasOwnProperty，这里有一些代码可以清除它。
```js
    function Animal (name, energy) {
      this.name = name
      this.energy = energy
    }

    Animal.prototype.eat = function (amount) {
      console.log(`${this.name} is eating.`)
      this.energy += amount
    }

    Animal.prototype.sleep = function (length) {
      console.log(`${this.name} is sleeping.`)
      this.energy += length
    }

    Animal.prototype.play = function (length) {
      console.log(`${this.name} is playing.`)
      this.energy -= length
    }

    const leo = new Animal('Leo', 7)

    leo.hasOwnProperty('name') // true
    leo.hasOwnProperty('energy') // true
    leo.hasOwnProperty('eat') // false
    leo.hasOwnProperty('sleep') // false
    leo.hasOwnProperty('play') // false
```
### 检查对象是否是类的实例
有时您想知道对象是否是特定类的实例。为此，您可以使用`instanceof`运算符。用例非常简单，但如果您以前从未见过它，实际的语法有点奇怪。它的工作原理如下

```
    object instanceof Class
```
如果`object`是`Class`的实例，则上述语句将返回true，否则返回false。回到我们的`Animal`例子，我们有类似的东西。

```js
    function Animal (name, energy) {
      this.name = name
      this.energy = energy
    }

    function User () {}

    const leo = new Animal('Leo', 7)

    leo instanceof Animal // true
    leo instanceof User // false
```
它的`instanceof`工作方式是检查`constructor.prototype`对象原型链中是否存在。在上面的例子中，`leo instanceof Animal`是true因为`Object.getPrototypeOf(leo) === Animal.prototype`。另外，`leo instanceof User`是false因为`Object.getPrototypeOf(leo) !== User.prototype`。


### 创建新的不可知构造函数
你能发现下面代码中的错误吗？
```js
    function Animal (name, energy) {
      this.name = name
      this.energy = energy
    }

    const leo = Animal('Leo', 7)
```
即使是经验丰富的JavaScript开发人员有时也会因为上面的例子而被绊倒。因为我们正在使用我们之前学到的`pseudoclassical pattern`，所以当`Animal`调用构造函数时，我们需要确保使用new关键字调用它。如果我们不这样做，那么this关键字将不会被创建，也不会被隐式返回。

作为复习，注释掉的行是new在函数上使用关键字时幕后发生的事情。

```js
    function Animal (name, energy) {
      // const this = Object.create(Animal.prototype)

      this.name = name
      this.energy = energy

      // return this
    }
```
这似乎是一个非常重要的细节，让其他开发人员记住。假设我们正在与其他开发人员合作，我们是否有办法确保`Animal`始终使用new关键字调用构造函数？事实证明，这是通过使用我们之前学到的`instanceof`操作来实现的。

如果使用`new`关键字调用构造函数，那么构造函数体内部的`this`将是`instanceof`构造函数本身。这里可以聊很多。这是一些代码。

```js
    function Animal (name, energy) {
      if (this instanceof Animal === false) {
        console.warn('Forgot to call Animal with the new keyword')
      }

      this.name = name
      this.energy = energy
    }
```
现在不是仅仅向函数的使用者记录警告，如果我们重新调用该函数，但是这次使用new关键字怎么办？
```js
    function Animal (name, energy) {
      if (this instanceof Animal === false) {
        return new Animal(name, energy)
      }

      this.name = name
      this.energy = energy
    }
```
现在无论是否`Animal`使用`new`关键字调用它，它仍然可以正常工作。

### 重新创建Object.create
在这篇文章中，我们非常依赖于Object.create创建委托给构造函数原型的对象。此时，您应该知道如何Object.create在代码中使用，但有一件事您可能没有想到的是Object.create实际上是如何工作的。为了让你真正了解它是如何Object.create工作的，我们将自己重新创建它。首先，我们对如何Object.create运作了解多少？

1.  它接受一个类型为对象的参数。
2.  它创建一个对象，该对象在失败的查找中委托给参数对象。
3.  它返回新创建的对象。.

让我们从＃1开始吧。
```js
    Object.create = function (objToDelegateTo) {

    }
```
很简单。

现在＃2 - 我们需要创建一个对象，该对象将在失败的查找中委托给参数对象。这个有点棘手。为此，我们将使用我们对new关键字和原型如何在JavaScript中工作的知识。首先，在我们Object.create实现的主体内部，我们将创建一个空函数。然后，我们将该空函数的原型设置为等于参数对象。然后，为了创建一个新对象，我们将使用new关键字调用我们的空函数。如果我们返回新创建的对象，那么它也将完成＃3。


```js
    Object.create = function (objToDelegateTo) {
      function Fn(){}
      Fn.prototype = objToDelegateTo
      return new Fn()
    }
```
让我们来看看吧。

当我们创建一个新函数时，Fn在上面的代码中，它带有一个prototype属性。当我们用new关键字调用它时，我们知道我们将得到的是一个对象，它将在失败的查找中委托给函数的原型。如果我们覆盖函数的原型，那么我们可以决定在失败的查找中委托哪个对象。因此，在我们上面的示例中，我们Fn使用Object.create调用时传入的对象覆盖原型objToDelegateTo。

> 请注意，我们只支持Object.create的单个参数。官方实现还支持第二个可选参数，该参数允许您向创建的对象添加更多属性。

### 箭头 Functions
箭头函数没有自己的this关键字。因此，箭头函数不能是构造函数，如果您尝试使用new关键字调用箭头函数，它将抛出错误。

```js
    const Animal = () => {}

    const leo = new Animal() // Error: Animal is not a constructor
```
另外，因为我们在上面说明了pseudoclassical pattern不能与箭头函数一起使用，所以箭头函数也没有prototype属性。

```js
    const Animal = () => {}
    console.log(Animal.prototype) // undefined
```

## 链接
* https://tylermcginnis.com/beginners-guide-to-javascript-prototype/
