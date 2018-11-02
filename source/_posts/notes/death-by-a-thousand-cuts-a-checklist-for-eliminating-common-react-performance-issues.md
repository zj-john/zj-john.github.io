---
title: 告别一千次崩溃,一个消除常见React性能问题的清单
categories:
  - notes
tags:
  - JavaScript Weekly
  - web development
  - javascript
  - react
  - 2018.10.12
toc: true
date: 2018-10-15 15:49:15
---

一个务实的逐步指导，以消除常见的反应性能问题。
A pragmatic step-by-step guide to eliminating common react performance issues.

![Death by a thousand cuts - a checklist for eliminating common React performance issues](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/react_performance_web-1.jpg)

<!-- more -->

你肯定想过怎么让你的React应用更快，你会列一个常用的用于检查性能的清单，然后逐一去排查吗？

在本文中，我将逐步向您介绍一个个实用指导，以消除常见的react性能问题。

首先，我将向您展示这些常见的性能问题，然后为他们提供解决方案。您可以讲相同的排查过程应用到您的实际项目。

本文不是冗长的理论，而是一个个可以快速在您的应用程序中马上使用的建议。

让我们开始吧！

### 示例项目
为了使本文尽可能实用，我将在一个React应用程序中引导您处理各种情况。

我把这个应用叫做 _[Cardie](https://github.com/ohansemmanuel/Cardie-performace)_.

![The Cardie App](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_W3WPox9JOFwplqjeu3KA_A.png)

这里是[Github repo](https://github.com/ohansemmanuel/Cardie-performace)地址。

Cardie是一个简单的应用程序。它所做的就是显示用户的个人资料信息。

![The User Details Being Displayed](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_Yh4h7wXPOcbaDBKdVJxm-g.png)

此外，通过单击按钮可以更改用户的职业。

![Cardie in Action](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_o-8SuFxH2Tl37JG-wSOj0g.gif)

单击应用程序底部的按钮后，用户的职业就会更改。

你可能会嘲笑这是一个没有任何现实意义且简单的应用程序，但看下去你可能会惊讶地发现通过这个示例中获得的追捕性能问题所获得的知识适用于任何真实的应用程序。

所以，保持冷静，继续阅读。

下面是要检查的清单！

### 1\. 识别浪费的渲染

识别react应用程序中的渲染浪费是识别大多数性能问题的完美开端。

有几种不同的方法可以解决这个问题，但最简单的方法是在React开发工具中切换“突出显示更新”选项。

![How to enable the highlight update toggle in React devtools](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_jfeS12BHr2dt5ooLeh5u3Q.gif)


这个选项可以使应用程序交互时，屏幕上会以绿色闪烁突出显示更新。

绿色闪烁的框框显示了应用程序中由React重新呈现的组件。

可以看到，在Cardie中，更改用户职业时，似乎整个父组件`App`都重新渲染了。

![Note the green flash around the user card](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1*ofXx-J4tr7KXXlNozuW9OA.gif)


这看起来有点问题。

虽然应用程序运行正常，但是当要更新的只有一小部分时，实际上没有理由重新渲染整个组件。

![The actual update happens in a small part of the App.](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_CDKifpFH46QkTzYgIfX3Cg.png)


更理想的效果应如下所示：

![Note how the highlighted update is contained within the small updated region.](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_Poo-6-5-bfYQ0qYBcXoyiw.gif)


在更复杂的应用程序中，浪费的渲染的影响可能是巨大的！因为重新渲染的组件可能大到足以促进性能问题。

针对这个问题，有什么解决方案吗？


### 2\. 将经常更新的区域提取为单独的组件
一旦您在应用程序中直观地注意到渲染的浪费，一个好的解决方式是尝试拆分组件树以隔离经常更新的部分。


在Cardie中，`App`组件通过`react-redux`的`connect`函数连接到redux store。从store中，它接收到这些属性：`name`, `location`, `likes` 和 `description`。

![<App/> receives the props it needs directly from the redux store.](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_EQLJ8lXEchp_USFfj2Em7A.png)


其中`description`属性表示用户的当前职业。

基本上，正在发生的事情是，只要通过单击按钮更改用户职业，就会更改`description`属性。然后，props中的这种更改会导致`App`组件重新渲染。

对React组件，只要组件的`props`或`state`发生更改，就会触发重新渲染。

![](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_rv3Y1Au-GarKtoiTJSmWRA.png)

![](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_x6NfWjAOcqwoZs2GhbdTZw.png)

![A React component renders a tree of elements. These elements are defined via props and state. If the props or state values changes, the tree of elements is re-rendered. This results in a new tree.](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_EpSSnv8Wjql-IcrNvzvz_A.png)

为了不使组件无意义地重新渲染，我们把要更新的元素改为特定的React组件。

例如，我们可以创建一个新的组件`Profession`，该组件渲染自己的DOM元素。

![](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_g8w3av5rclzcX9N387nxgQ.png)

此时，`Profession`组件将呈现用户职业的描述，例如“我是编码员”。

![The <Profession/> component will be rendered in the <App/> component.](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_obGgPxv-C9XMjtxEc1x8xg.png)

组件树现在看起来像这样：

![The <App/> component renders the elements it renders plus the <Profession/> component.](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_0kqtSR4WxvGy4yYBKyReIw.png)


这里的重点是，我们把`<App/>`下的`profession`属性问题，变成了`<Profession/>`组件的问题。

![The profession will now be retrieved directly from the redux store by the <Profession/> component.](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_tLbbgUgBavBm6FMpOzqL7Q.png)

无论你是否使用`redux`，这里的要点是`App`不再由于`profession`属性的变化而重新渲染，但`<Profession/>`组件将是。

![Note how the highlighted updates is contained within <Profession />](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_Poo-6-5-bfYQ0qYBcXoyiw.gif)


要查看代码更改，请参阅repo中的[isolated-component branch](https://github.com/ohansemmanuel/Cardie-performace/tree/isolated-component)分支。

### 3\. 适当使用纯组件
任何关于React性能的文章都很可能会提到纯组件。

但是，什么情况下需要使用[纯组件](https://reactjs.org/docs/react-api.html#reactpurecomponent)呢？

理论上，你可以把每个组件都变成纯组件，但是请记住，使用了`shouldComponentUpdate`函数的组件不在情况之列。

成为纯组件的前提是，当且仅当组件的`props`与先前的props和state不同时，组件才重新渲染。

构建纯组件的简单方法是使用`React.PureComponent`，而不是默认的 `React.Component`

![Using React.PureComponent as opposed to React.Component](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_VrdjOEthHOb6MWxE6lDc0A.png)


针对 _Cardie_ 的特定情况，让我们将`Profession`组件的渲染元素分解为更小的组件。

所以，这是`Profession`之前的内容：

```js
    const Description = ({ description }) => {
      return (
        <p>
         <span className="faint">I am</span> a {description}
        </p>
      );
    }
```
然后我们将其更改为：
```js
    const Description = ({ description }) => {
      return (
        <p>
          <I />
          <Am />
          <A />
          <Profession profession={description} />
        </p>
      );
    };
```
现在，该`Description`组件涵盖了4个子组件。

![](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_TpXci0NJ24imQqmsc7S-_A.png)

请注意，`Description`组件接收`profession`属性。然后，它只将此属性传递给`Profession`组件。所以从技术上讲，其他3个组件都不关心这个`profession`属性。

这些新组件的内容非常简单。例如，`<I />`组件只返回带有“I”文本的`span`元素：`<span >I </span>`

运行一下，结果正常。

有趣的是，在改变`description`属性时，每个`Description`的子组件也会被重新渲染。

![Once a new prop value is passed into the Description component, all the child components also re-render.](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_LDQJckpn733R8cgflYTRSg.png)


我在每个子组件的`render`方法中添加了一些日志- 正如您所看到的，它们确实都被重新渲染。

![](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_s4r-atPSx7F_tKLFj5v-gg.png)

您还可以使用react开发工具高亮显示更新的部分。

![Note how there are green flashes around each of the words, I, am and a.](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_34bdT5PCFRmwTpmgo7qZPw.gif)

这个结果是可以解释的。当组件`props`或`state` 更改时，它呈现的元素树将被重新计算，即重新渲染。

在这个特定的例子中，我们都知道`<I/>`, `<Am/>` 和 `<A/>`子组件被重新渲染是完全没有意义的，虽然，`<Description/>`中的`props`确实更改了。试想，如果这是一个足够大的应用程序，则此行为可能会造成一些性能威胁。

如果我们将这些子组件转换为纯组件，情况会不会好一点呢？

考虑`<I/>`组件：

```js
  import React, {Component, PureComponent} from "react"

  //before
  class I extends Component {
    render() {
      return <span className="faint">I </span>
    };
  }

  //after
  class I extends PureComponent {
    render() {
      return <span className="faint">I </span>
    };
  }

```

通过这种写法，等同于在后台通知React：这样如果这些子组件的prop值没有改变，就不需要重新渲染它们。

是的，即使父元素的属性发生了变化，也不要重新渲染它们！

![](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_ETCitcco6c-lSIm5Fbfu0w.png)

在重构后，您可以看到其它子组件不再重新渲染了。只重新渲染了实际更改的`prop`的`Profession`组件。

![There are no flashes around the words, “I”, “am” and “a”. Just the overall container, and the profession flashes.](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1*UK66j52zr2ezTDhTLtZWGg.gif)

在更大的应用程序中，您可以通过仅使某些组件成为纯组件来发现巨大的性能优化。

要查看代码更改，请参阅repo中的[pure-component branch](https://github.com/ohansemmanuel/Cardie-performace/tree/pure-components)分支。

### 4\. 避免将新对象作为属性传递
再次记住，只要`props`发生变化，组件就会发生重新渲染。

![If the props or state values changes, the tree of elements is re-rendered. This results in a new element tree.](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_EpSSnv8Wjql-IcrNvzvz_A.png)

如果您的组件`props`没有改变但React认为它确实改变了怎么办？

好吧，这也会导致一次重新渲染！

但这不是很奇怪吗？

这种看似奇怪的行为是因为Javascript的工作原理以及React如何处理新老prop值之间的比较而发生的。

让我举个例子。

这是`Description`组件的内容：

```js
    const Description = ({ description }) => {
      return (
    	<p>
    	   <I />
    	   <Am />
    	   <A />
     	   <Profession profession={description} />
    	</p>
      );
    };
```
现在，我们将重构该`I`组件,使他需要一个确定的`i`属性。i是如下形式的对象：
```js
    const i = {
      value: "i"
    };
```
无论`i`是什么值，都将传递给`I`组件并显示。
```js
    class I extends PureComponent {
      render() {
        return <span className="faint">{this.props.i.value} </span>;
      }
    }
```
在`Description`组件中，`i`属性被创建并传入，如下所示：
```js
    class Description extends Component {
      render() {
    	const i = {
    	  value: "i"
    	};
    	return (
    	  <p>
          <I i={i} />
    	    <Am />
    	    <A />
    	    <Profession profession={this.props.description} />
    	  </p>
    	);
      }
    }
```
我会花点时间解释这个。

这是完全正确的代码，它也工作正常 - 但有一个问题。

即使`I`是纯组件，但只要用户的职业发生变化，它就会重新渲染！

![On Clicking the button, the logs show that both <I/> and <Profession/> are re-rendered. Remember there’s been no actual props change in <I/>. Why the re-render?](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1*NymA8dLgtXQIHhk7AI2LzQ.png)


这是为什么？

一旦`Description`组件接收新的属性，`render`函数就被调用来创建它的元素树。

在调用render函数时，它会创建一个新的`i`常量：
```js
    const i = {
      value: "i"
    };
```
当React评估`<I i={i} />`这行代码时，它会将属性`i`视为不同的属性，一个新对象 - 因此重新渲染。

React会对上一个和下一个属性进行浅层比较。

即标量值（如字符串和数字）按值进行比较。对象通过引用比较。

也就是说，即使常量`i`在重新渲染之间具有相同的值，但它们的引用也不相同，因为在内存中的地址变了。

所以，每次渲染时都会新创建对象。因此，`prop`传递给`<I/>`的值被视为“新”，因此重新渲染。

在大型应用程序中，这可能会导致渲染浪费和潜在的性能缺陷。

避免这样做。

这适用于每个`prop`包括事件处理程序。

如果你可以避免它，你不应该这样做：
```
    ...
    render() {
      <div onClick={() => {//do something here}}
    }
    ...
```
因为，您每次在渲染中创建一个新的函数对象。下面这样做更好：
```
    ...
    handleClick:() ={
    }
    render() {
      <div onClick={this.handleClick}
    }
    ...
```
了解了吗？

同样，我们可以重构`<I />`中传入属性的方式，如下所示：
```js
    class Description extends Component {
      i = {
        value: "i"
      };
      render() {
        return (
          <p>
           <I i={this.i} />
           <Am />
           <A />
           <Profession profession={this.props.description} />
          </p>
        );
      }
    }
```
现在，引用总是一样的，都是`this.i`。

在渲染时也不会创建新对象。

要查看代码更改，请从repo中查看[new-objects branch](https://github.com/ohansemmanuel/Cardie-performace/tree/new-objects)分支。

### 5\. 使用生产版本
部署到生产时，始终使用生产构建。这是一个非常简单但很棒的做法。

![The “development build” warning you get with the react devtools in development.](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1*gHK6-tjRML2CA7fquzH6CQ.png)

如果您已经使用`create-react-app`引导您的应用，运行生产版本请使用以下命令：`npm run build`。

这将生成用于生产的优化的打包文件。

### 6\. 使用代码拆分
打包应用程序时，您可能将整个应用程序打包在一个大块中。

这个问题是随着你的应用程序的增长，打的包也会越来越大。

![Once the user visits the site, he is sent a large chunk of code for the entire app.](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1*sJpQeFIkxgEQxGKW1ov6JA.png)


代码拆分主张不是一次将大块代码直接发送给用户，而是在需要时动态地向用户发送。

一个常见的例子是基于路由的代码拆分。在此方法中，代码根据应用程序中的路由拆分为块。

![The /home route gets a small chunk of code, so does the /about route.](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_VH5tRBxSSUrPAjf93CCqJw.png)


另一种方法是基于组件的代码分割。在此方法中，如果当前组件未显示，则可能会把其代码延迟发送给用户。

无论您坚持哪种方法，都必须了解权衡因素并且不会降低应用程序的用户体验。

代码拆分很棒，它可以提高应用程序的性能。

我只是笼统的解释了代码分割。如果您需要更多技术支持，请查看官方的[React文档](https://reactjs.org/docs/code-splitting.html)。他们在解释技术概念方面做得不错。

### 结论

现在，您已经有了一个像样的清单来跟踪和修复react应用中的常见性能问题。去构建一些更加快速的应用程序吧！

![](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/minimal-rocket-2xA-1.png)

## 资源
* 原文 https://logrocket-blog.ghost.io/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/
* Github https://github.com/ohansemmanuel/Cardie-performace
