---
title: 自定义元素简介
categories:
  - notes
  - customelements
  - javascript
  - html
  - 2018.10.05
tags:
  - JavaScript Weekly
toc: true
date: 2018-10-11 15:48:15
---

## 介绍

自定义元素是Web Components的一个子集，在我看来是最酷的网络功能之一。实际上，它们允许我们构建原生Web平台的组件，而不是使用React，Angular，Vue等中间库或框架。

在这篇文章中，我希望向您展示自定义元素是什么，如何构建它以及如何将它放在页面上。

<!-- more -->

## Web组件的基础

所有自定义元素都将包含一些常用方法，可以在下面的代码示例中看到一个简单例子：
```js
   class MyComponent extends HTMLElement {
     static get observedAttributes() {
       return [];
     }

     constructor(...args) {
       super(...args);
     }

     connectedCallback() {}

     disconnectedCallback() {}

     adoptedCallback() {}

     attributeChangedCallback(attrName, oldVal, newVal) {}
   }

   window.customElements.define('my-component', MyComponent);
```

让我们逐步分析一下。

### constructor()

>理论上，构造函数必须声明，并将接收到的所有参数传递给父级。

理想情况下，构造函数中通常会绑定所有的事件侦听器，例如：
```js
   ...
   constructor(...args) {
       super(...args);
       this.addEventListener('click', this.handleClick);
   }

   handleClick(event) {}
   ...
```

### connectedCallback()

>每次将元素插入DOM时调用。

每次将组件添加到页面中时，它都会触发此函数。

### disconnectedCallback()

>每次从DOM中删除元素时调用。

例如，如果我们在DOM树中的删除该节点或其父节点，则此函数将触发。

当自定义元素在文档或在新页面的其他地方采用时，`disconnectedCallback()`也会运行。

### adoptedCallback()

> 每次将自定义元素移动到新文档时调用。

如果将自定义元素移动到新页面或文档，则将触发此回调。（当前没有API可以直接调用此函数的，因为已有的Dom的操作都是先删后增，不存在直接移动的情况）

### attributeChangedCallback(attrName, oldVal, newVal)

>添加，删除，更新或替换元素的属性时会发生此行为。

组件上的任意属性变化，都会触发此函数。但是，只有当被处于观察状态的属性发生变化时，才会触发`observedAttributes()`。

### observedAttributes()

>自定义元素中被观察的属性发生变化时触发

正如你所看到的，这个方法被声明为`static get observedAttributes()`，这明显不同于其他方法声明，这是因为我们希望它不被任何子类/组件继承，我们只想在引用它的时候声明一次，注意，它是`static`和`get`方式（供参考）。

此函数应返回一个字符串数组，其中每个字符串都是您要观察的属性的名称，例如：
```js
   ...
   static get observedAttributes() {
       return ['id', 'my-custom-attribute', 'data-something', 'disabled'];
   }
   ...
```
自定义元素规范中还有一些其他功能，但上面这些功能是我们日常主要使用的功能。

## 一个简单的组件Demo
让我们构建一个向用户打招呼的基本组件。

### The html
```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <meta http-equiv="X-UA-Compatible" content="ie=edge">
     <title>Custom Elements 101</title>
   </head>
   <body>
     <hello-component data-name="James"></hello-component>
     <script src="./hello-component.js"></script>
   </body>
   </html>
```

### The javascript
```js
   class HelloComponent extends HTMLElement {
     static get observedAttributes() {
       return ['data-name'];
     }

     // custom methods
     render() {
       this.innerHTML = `Hello ${this.name}`;
     }

     get name() {
       return this.getAttribute('data-name');
     }

     // lifecycle hooks
     connectedCallback() {
       this.render();
     }

     attributeChangedCallback(attrName, oldVal, newVal) {
       this.render();
     }
   }

   // add into the 'real' dom as a valid tag
   window.customElements.define('hello-component', HelloComponent);
```

加载index.html，您应该会在页面上看到“Hello James”。

现在，打开浏览器的DevTools，并将`data-name`属性更改为除James之外的其他属性。你会发现实时更新！很棒吧？

当然，这只是一个非常基本的，非最佳实践，0用例，默认教程实现，但它确实为您提供了一个粗略的介绍，以便我们可以在以后的文章中构建。

## 浏览器支持
下图是当前主流浏览器对Web组件以及所有支持它们的API，包括Shadow DOM，自定义元素（我们刚刚查看的内容），HTML模板和插槽以及HTML导入的支持情况：

![Current support for Web Components APIs from the caniuse documentation at the time of writing this article](/images/JS_Weekly/an-introduction-to-custom-elements/Capture.png)

## 结论
自定义元素允许我们在*需要*时以无框架的方式实现实时更新UI 。它们确实为我们提供了很多挑战，其中许多将在未来展望，但是请先尝试一下，看看其他Web组件API，就像放在一起时，它们真的让我们变得更酷，更强大，可以用事倍功半。


## 资源
* 原文：https://dev.to/jamesrweb/an-introduction-to-custom-elements-5327
* [Web组件 - MDN](https://developer.mozilla.org/en-US/docs/Web/Web_Components)
* [自定义元素 - MDN](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_custom_elements)
* [Shadow DOM - MDN](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_shadow_DOM)
* [HTML Templates and Slots - MDN](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_templates_and_slots)
