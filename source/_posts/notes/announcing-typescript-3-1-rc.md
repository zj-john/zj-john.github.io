---
title: TypeScript 3.1 RC上线【译】
categories:
  - notes
tags:
  - JavaScript Weekly
toc: true
date: 2018-09-17 20:12:01
---

今天[2018-09-13]，我们很高兴地宣布TypeScript 3.1的候选版本（RC）可用了。发布RC的目的是为了收集任何意见和建议，以便确保我们正式版本的发布尽可能顺利。

如果您现在想试一试，可以通过[NuGet](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild)获取RC ，或使用以下命令使用npm：

```npm
npm install -g typescript @ rc
```

以下编译器也已支持RC版本

*   下载[Visual Studio 2017](http://download.microsoft.com/download/7/0/A/70A6AC0E-8934-4396-A43E-445059F430EA/3.1.0-TS-release-dev14update3-20180911.1/TypeScript_SDK.exe)（适用于15.2或更高版本）
*   下载[Visual Studio 2015](http://download.microsoft.com/download/6/D/8/6D8381B0-03C1-4BD2-AE65-30FF0A4C62DA/3.1.0-TS-release-dev14update3-20180911.1/TypeScript_Dev14Full.exe)（使用[Update 3](https://www.visualstudio.com/en-us/news/releasenotes/vs2015-update3-vs)）
*   遵循[Visual Studio Code](https://code.visualstudio.com/Docs/languages/typescript#_using-newer-typescript-versions)和[Sublime Text](https://github.com/Microsoft/TypeScript-Sublime-Plugin/#note-using-different-versions-of-typescript)的说明。

让我们来看看TypeScript 3.1中会出现什么！

<!-- more -->

## 可映射的元组和数组类型

遍历列表中的值是编程中最常见的模式之一。作为示例，我们来看看以下JavaScript代码：

```js
function stringifyAll(...elements) {
    return elements.map(x => String(x));
}
```

该`stringifyAll`函数接受任意数量的值，将每个元素转换为字符串，并将结果放在一个新数组中，然后返回该数组。  
如果我们希望支持各种类型转换的`stringifyAll`，我们可以将其声明为：


```ts
declare function stringifyAll(...elements: unknown[]): Array<string>;
```

这个声明表示，“这个东西需要任意数量的元素，并返回一个`string`类型的数组”; 但是，我们`elements`在这次转型中失去了一些信息。

具体来说，类型系统不记得用户传入的元素数量，因此我们的输出类型也没有已知的长度。我们可以通过重载做类似的事情：

```ts
declare function stringifyAll(...elements: []): string[];
declare function stringifyAll(...elements: [unknown]): [string];
declare function stringifyAll(...elements: [unknown, unknown]): [string, string];
declare function stringifyAll(...elements: [unknown, unknown, unknown]): [string, string, string];
// ... etc
```

呃。我们甚至没有涵盖_四个_要素。你最终会特别容纳所有这些可能的重载，你最终会得到我们所谓的“一千次重载导致的死亡”问题。当然，我们可以使用条件类型而不是重载，但是你会有一堆嵌套的条件类型。

如果只有一种方法可以在这里统一映射每种类型......

好吧，TypeScript已经有了类似的功能。TypeScript有一个称为映射对象类型的概念，它可以从现有类型中生成新类型。例如，给定以下`Person`类型，


```ts
interface Person {
    name: string;
    age: number;
    isHappy: boolean;
}
```

我们可能希望将每个属性转换为如上所示的字符串：

```ts
interface StringyPerson {
    name: string;
    age: string;
    isHappy: string;
}

function stringifyPerson(p: Person) {
    const result = {} as StringyPerson;
    for (const prop in p) {
        result[prop] = String(p[prop]);
    }
    return result;
}
```

虽然通知`stringifyPerson`很普遍。我们可以`Stringify`使用映射对象类型在任何给定类型的属性上抽象出-ing类型的概念：

```ts
type Stringify<T> = {
    [K in keyof T]: string
};
```

对于那些不熟悉，我们看这是“为每一个命名的属性`K`中`T`，产生与类型名称的新特性`string`。

并重写我们的函数来使用它：

```ts
function stringifyProps<T>(p: T) {
    const result = {} as Stringify<T>;
    for (const prop in p) {
        result[prop] = String(p[prop]);
    }
    return result;
}

stringifyProps({ hello: 100, world: true }); // has type `{ hello: string, world: string }`
```

好像我们有我们想要的东西！但是，如果我们尝试更改类型`stringifyAll`以返回`Stringify`：

```ts
declare function stringifyAll<T extends unknown[]>(...elements: T): Stringify<T>;
```

然后尝试在数组或元组上调用它，我们只能获得在TypeScript 3.1之前_几乎_有用的东西。让我们来试试像旧版本的TypeScript 3.0：

```ts
let stringyCoordinates = stringifyAll(100, true);

// No errors!
let first: string = stringyCoordinates[0];
let second: string = stringyCoordinates[1];
```

看起来我们的元组索引已正确映射！让我们现在检查抓住长度并确保正确：

```ts
let len: 2 = stringyCoordinates.length
//     ~~~
// Type 'string' is not assignable to type '2'.
```

呃。`string`？好吧，让我们尝试迭代我们的坐标。

```ts
stringyCoordinates.forEach(x => console.log(x));
// ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
// Cannot invoke an expression whose type lacks a call signature. Type 'String' has no compatible call signatures.
```

咦？导致此严重错误消息的原因是什么？那么我们的`Stringify`映射类型不仅映射了我们的元组成员，它还映射了方法`Array`以及`length`属性！所以`forEach`，`length`两者都有类型`string`！

虽然在技术上一致的行为，我们的大多数团队认为这个用例应该只是工作。在迭代元组和数组时，映射对象类型现在只是“做正确的事”，而不是引入用于映射元组的新概念。这意味着，如果你已经使用现有的映射类型，如`Partial`或`Required`从`lib.d.ts`，他们就自动元组和数组现在的工作。

## 函数声明的属性

在JavaScript中，函数只是对象。这意味着我们可以随意将属性粘贴到它们上面：

```ts
export function readFile(path) {
    // ...
}

readFile.async = function (path, callback) {
    // ...
}
```

TypeScript的传统方法是一种非常通用的结构，称为`namespace`s（如果你已经足够老了，也称为“内部模块”）。除了组织代码之外，命名空间还支持_值合并_的概念，您可以以声明方式向类和函数添加属性：

```ts
export function readFile() {
    // ...
}

export namespace readFile {
    export function async() {
        // ...
    }
}
```

虽然他们的时间可能很优雅，但这种结构并没有很好地老化。ECMAScript模块已成为在更广泛的TypeScript和JavaScript社区中组织新代码的首选格式，名称空间是特定于TypeScript的。此外，名称空间不与`var`，`let`或`const`声明合并，因此代码如下（由`defaultProps`React驱动）：

```ts
export const FooComponent => ({ name }) => (
    <div>Hello! I am {name}</div>
);

FooComponent.defaultProps = {
    name: "(anonymous)",
};
```

甚至不能简单地转换成

```ts
export const FooComponent => ({ name }) => (
    <div>Hello! I am {name}</div>
);

// Doesn't work!
namespace FooComponent {
    export const defaultProps = {
        name: "(anonymous)",
    };
}
```

所有这些都可能令人沮丧，因为它更难以迁移到TypeScript。

鉴于所有这些，我们认为让TypeScript对这些类型的模式更“智能”会更好。在TypeScript 3.1中，对于使用函数`const`初始化的任何函数声明或声明，类型检查器将分析包含的范围以跟踪任何添加的属性。这意味着两个示例 \- 无论`readFile`是我们的`FooComponent`示例还是我们的示例 \- 在TypeScript 3.1中无需修改即可运行！

作为一个额外的好处，此功能与TypeScript 3.0的支持相结合，`JSX.LibraryManagedAttributes`使得无类型的React代码库迁移到TypeScript 变得_非常_容易，因为它了解哪些属性在以下情况下是可选的`defaultProps`：

```ts
// TypeScript understands that both are valid:
<FooComponent />
<FooComponent name="Nathan" />
```

## 突破性变化

我们的团队总是努力避免引入重大变化，但不幸的是有些人需要注意TypeScript 3.1。

### 已删除特定于供应商的声明

TypeScript 3.1现在`lib.d.ts`使用WHATWG DOM规范提供的Web IDL文件生成部分（和其他内置声明文件库）。虽然这意味着`lib.d.ts`更容易保持最新，但许多特定于供应商的类型已被删除。我们在[维基上](https://github.com/Microsoft/TypeScript/wiki/Breaking-Changes)更详细地[介绍了](https://github.com/Microsoft/TypeScript/wiki/Breaking-Changes)这一点。

### 缩小功能的差异

使用`typeof foo === "function"`型保护用的由相对可疑联合类型相交时可提供不同的结果`{}`，`Object`或不受约束泛型。

```ts
function foo(x: unknown | (() => string)) {
    if (typeof x === "function") {
        let a = x()
    }
}
```

您可以在[我们的wiki的重大更改部分](https://github.com/Microsoft/TypeScript/wiki/Breaking-Changes#narrowing-functions-now-intersects--object-and-unconstrained-generic-type-parameters)阅读更多内容。

## 未来

我们期待听到您对RC的体验。与往常一样，请密切关注[我们的路线图](https://github.com/Microsoft/TypeScript/wiki/Roadmap)，以便在我们稳定时全面了解发布情况。我们希望在短短几周内发布我们的最终版本，所以现在就试试吧！

## 原文地址
https://blogs.msdn.microsoft.com/typescript/2018/09/13/announcing-typescript-3-1-rc/
