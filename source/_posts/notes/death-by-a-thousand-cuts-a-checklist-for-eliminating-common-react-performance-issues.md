---
title: "告别一千次崩溃",一个消除常见React性能问题的清单
categories:
  - notes
  - web development
  - javascript
  - react
  - 2018.10.12
tags:
  - JavaScript Weekly
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

Let me show you what I mean.

In Cardie, the `App` component is connected to the redux store via the `connect` function from `react-redux`. From the store, it receives the props: `name`, `location`, `likes` and `description`.

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

无论你是否使用`redux`，这里的要点是`App`不再由于`profession`属性的变化而重新渲染，但<Profession/>组件将是。

![Note how the highlighted updates is contained within <Profession />](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_Poo-6-5-bfYQ0qYBcXoyiw.gif)


要查看代码更改，请参阅repo中的[isolated-component branch](https://github.com/ohansemmanuel/Cardie-performace/tree/isolated-component)分支。

### 3\. 适当使用纯组件
任何关于React性能的文章都很可能会提到纯组件。

但是，什么情况下需要使用[纯组件](https://reactjs.org/docs/react-api.html#reactpurecomponent)呢？

理论上，你可以把每个组件都变成纯组件，但是请记住，为什么不是开箱即用的原因。因此`shouldComponentUpdate`功能。
Well, it is true that you could make every component a pure component, but remember there’s a reason why that isn’t the case out of the box. Hence the`shouldComponentUpdate` function.

成为纯组件的前提是，当且仅当组件的`props`与先前的props和state不同时，组件才重新渲染。

构建纯组件的简单方法是使用`React.PureComponent`，而不是默认的 `React.Component`

![Using React.PureComponent as opposed to React.Component](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_VrdjOEthHOb6MWxE6lDc0A.png)


为了说明 _Cardie_ 中的这个特定用例，让我们将`Profession`组件的渲染元素分解为更小的组件。

所以，这是Profession之前的内容：
To illustrate this specific use case in _Cardie_, let’s break down the render elements of the `Profession` component into smaller components.

So, this was the content of `Profession` before now:
```js
    const Description = ({ description }) => {
      return (
        <p>
         <span className="faint">I am</span> a {description}
        </p>
      );
    }
```
以下是我们将其更改为：
Here’s what we’ll change it to:
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
现在，该Description组件呈现4个子组件。
Now, the `Description` component renders 4 children components.

![](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_TpXci0NJ24imQqmsc7S-_A.png)

请注意，Description组件接受professionprop。但是，它将此prop传递给Profession组件。从技术上讲，其他3个组件都不关心这个profession道具。

这些新组件的内容非常简单。例如，<I />组件只返回span带有“I”文本的元素：span >I </span>

如果应用程序运行，结果是一样的。该应用程序工作。

有趣的是，在改变description道具时，每个儿童成分Profession也会被重新渲染。
Note that the Description component takes in a `profession` prop. However, it passes on this prop to the `Profession` component. Technically, none of the other 3 components care about this `profession` prop.

The contents of these new components are super simple. For example, the `<I />` component just returns a `span` element with an “I” text: `span >I </span>`

If the application runs, the result is just the same. The app works.

What’s interesting is that upon a change to the `description` prop, every child component of `Profession` is also re-rendered.

![Once a new prop value is passed into the Description component, all the child components also re-render.](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_LDQJckpn733R8cgflYTRSg.png)


我在render每个子组件的方法中添加了一些日志- 正如您所看到的，它们确实都被重新渲染。
I added a few logs in the `render` methods of each child component - and as you can see they were indeed all re-rendered.

![](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_s4r-atPSx7F_tKLFj5v-gg.png)

您还可以使用react开发工具查看突出显示的更新。
You may also view the highlighted updates using the react dev tools.

![Note how there are green flashes around each of the words, I, am and a.](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_34bdT5PCFRmwTpmgo7qZPw.gif)


此行为是预期的。每当组件具有props或state更改时，它呈现的元素树将被重新计算。这与重新渲染同义。

在这个特定的例子中，你会同意我的意见，如果真的没有意义<I/>，<Am/>并且<A/>子组件要重新渲染。是的，props父元素中的元素已<Description/> 更改，但如果这是一个足够大的应用程序，则此行为可能会造成一些性能威胁。

如果我们将这些子组件制成纯组件怎么办？

考虑<I/>组件：
This behavior is expected. Whenever a component has either `props` or `state` changed, the tree of elements it renders is recomputed. This is synonymous to a re-render.

In this particular example, you’ll agree with me that if really makes no sense for `<I/>`, `<Am/>` and `<A/>` child components to be re-rendered. Yes, the `props` in the parent element, `<Description/>`  changed, but if this were a sufficiently large application, this behaviour may pose some performance threats.

What if we made these child components pure components?

Consider the `<I/>` component:

```
  import React, {Component, PureComponent} from "react"

    //before
    class I extends Component {
      render() {
        return <span className="faint">I </span>;
    }

    //after
    class I extends PureComponent {
      render() {
        return <span className="faint">I </span>;
    }

```

通过暗示，在引擎盖下通知React，这样如果这些子组件的prop值没有改变，就不需要重新渲染它们。

是的，即使父元素的道具发生了变化，也不要重新渲染它们！
By implication, React is informed under the hood so that if the prop values for these child components aren’t changed, there’s no need to re-render them.

Yes, do not re-render them even when the parent element has a change in its props!

![](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_ETCitcco6c-lSIm5Fbfu0w.png)

在重构后检查突出显示的更新时，您可以看到不再重新呈现子组件。只是重新渲染实际更改的Profession组件prop。
Upon inspecting the highlighted updates after the refactor, you can see that the child components are no longer re-rendered. Just the `Profession` component whose `prop` actually changes is re-rendered.

![There are no flashes around the words, “I”, “am” and “a”. Just the overall container, and the profession flashes.](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1*UK66j52zr2ezTDhTLtZWGg.gif)

在更大的应用程序中，您可以通过仅使某些组件成为纯组件来发现巨大的性能优化。

要查看代码更改，请参阅repo中的pure-component分支。

In a larger application, you may find immense performance optimizations by just making certain components pure components.

To view the code change, please see the [pure-component branch](https://github.com/ohansemmanuel/Cardie-performace/tree/pure-components) from the repo.

### 4\. Avoid passing new objects as props 避免将新对象作为道具传递
再次记住，只要props组件发生变化，就会发生重新渲染。
Remember again that whenever the `props` for a component changes, a re-render happens.

![If the props or state values changes, the tree of elements is re-rendered. This results in a new element tree.](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_EpSSnv8Wjql-IcrNvzvz_A.png)


如果props您的组件没有改变但React认为它确实改变了怎么办？

好吧，还有一个重新渲染！

但这不是很奇怪吗？

这种看似奇怪的行为是因为Javascript如何工作以及React如何处理旧的和新的prop值之间的比较而发生的。

让我举个例子。

这是Description组件的内容：
What if the `props` for your component didn’t change but React thinks it did change?

Well, there’ll also be a re-render!

But isn’t that weird?

This seemingly weird behavior happens because of how Javascript works & how React handles its comparison between old and new prop values.

Let me show you an example.

Here’s the content for the `Description` component:
```
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
现在，我们将重构该I组件以获取某个i道具。这将是以下形式的对象：
Now, we will refactor the `I` component to take in a certain `i` prop. This will be an object of the form:
```
    const i = {
      value: "i"
    };
```
无论存在什么值属性，i都将设置为I组件中的值。
Whatever value property is present in `i` will be set as the value in the `I`component.
```
    class I extends PureComponent {
      render() {
        return <span className="faint">{this.props.i.value} </span>;
      }
    }
```
在Description组件中，iprop被创建并传入，如下所示：
In the `Description` component, the `i` prop is created and passed in as shown below:
```
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
当我解释这个时，请耐心等待。

这是完全正确的代码，它工作正常 - 但有一个问题。

即使I是纯粹的组件，现在只要用户的职业发生变化，它就会重新呈现！
Bear with me while I explain this.

This is perfectly correct code, and it works fine — but there is one problem.

Even though `I` is a pure component, now it re-renders whenever the profession of the user is changed!

![On Clicking the button, the logs show that both <I/> and <Profession/> are re-rendered. Remember there’s been no actual props change in <I/>. Why the re-render?](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1*NymA8dLgtXQIHhk7AI2LzQ.png)


但为什么？

一旦Description组件接收新的道具，render函数被调用来创建它的元素树。

在调用render函数时，它会创建一个新的i常量：
But why?

As soon as the `Description` component receives the new props, the `render`function is called to create its element tree.

Upon invoking the render function it creates a new `i` constant:
```
    const i = {
      value: "i"
    };
```
当React得到评估线时 <I i={i} />，它会将道具i视为不同的道具，一个新对象 - 因此重新渲染。

如果你记得React 101，React会对前一个和下一个道具进行浅层比较。

标量值（如字符串和数字）按值进行比较。通过引用比较对象。

通过暗示，即使常量i在重新渲染之间具有相同的值，参考也不相同。记忆中的位置不是。

它是每个渲染调用的新创建的对象。因此，prop传递给的值<I/>被视为“新”，因此重新渲染。

在更大的应用程序中，这可能会导致浪费的渲染和潜在的性能缺陷。

避免这样做。

这适用于每个prop包括事件处理程序。

如果你可以避免它，你不应该这样做：
When React gets to evaluate the line, `<I i={i} />`, it sees the props `i` as a different prop, a new object — therefore the re-render.

If you remember from React 101, React does a shallow comparison between the previous and next props.

Scalar values such as strings and numbers are compared by value. Objects are compared by reference.

By implication, even though the constant `i` has the same value across re-renders, the reference is not the same. The position in memory isn’t.

It’s a newly created object with every single render call. For this reason, the `prop` value passed to `<I/>` is regarded as “new”, consequently a re-render.

In bigger applications, this can lead to a wasted render, and potential performance pitfalls.

Avoid this.

This applies to every `prop` including event handlers.

If you can avoid it, you shouldn’t do this:
```
    ...
    render() {
      <div onClick={() => {//do something here}}
    }
    ...
```
您每次在渲染中创建一个新的函数对象。这样做更好：
You’re creating a new function object every time within render. Better do this:
```
    ...
    handleClick:() ={
    }
    render() {
      <div onClick={this.handleClick}
    }
    ...
```
了解？

同样，我们可以重构发送到的道具<I />，如下所示：
Got that?

In the same vein, we may refactor the prop sent to `<I />` as shown below:
```
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
现在，引用总是一样的this.i。

在渲染时不会创建新对象。

要查看代码更改，请从repo中查看new-objects分支。
Now, the reference is always the same, `this.i`.

A new object isn’t created at render time.

To view the code change, please see the [new-objects branch](https://github.com/ohansemmanuel/Cardie-performace/tree/new-objects) from the repo.

### 5\. Use the production build 使用生产版本
部署到生产时，始终使用生产构建。这是一个非常简单但很棒的做法。
When deploying to production, always use the production build. This is a very simple, but great practice.

![The “development build” warning you get with the react devtools in development.](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1*gHK6-tjRML2CA7fquzH6CQ.png)


如果您已经使用create-react-app，以运行生产版本来引导您的应用程序，请使用以下命令：npm run build。

这将产生用于生产的捆绑优化文件。
If you have bootstrapped your application with `create-react-app` , to run the production build, use the command: `npm run build`.

This will yield bundle optimized files for production.

### 6\. Employ code splitting 使用代码拆分
捆绑应用程序时，您可能将整个应用程序捆绑在一个大块中。

这个问题是随着你的应用程序的增长，捆绑包也是如此。
When you bundle your application, you likely have the entire application bundled in one large chunk.

The problem with this is that as your app grows, so does the bundle.

![Once the user visits the site, he is sent a large chunk of code for the entire app.](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1*sJpQeFIkxgEQxGKW1ov6JA.png)


代码拆分主张不是一次将大块代码发送给用户，而是在需要时动态地向用户发送块。

一个常见的例子是基于路由的代码拆分。在此方法中，代码根据应用程序中的路由拆分为块。
Code splitting advocates that instead of sending this large chunk of code to the user at once, you may dynamically send chunks to the user when they need it.

A common example is with route based code splitting. In this method, the code is split into chunks based on the routes in the application.

![The /home route gets a small chunk of code, so does the /about route.](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/1_VH5tRBxSSUrPAjf93CCqJw.png)


另一种方法是基于组件的代码分割。在此方法中，如果当前未向用户显示组件，则可能会延迟其代码被发送给用户。

无论您坚持哪种方法，都必须了解权衡因素并且不会降低应用程序的用户体验。

代码拆分很棒，它可以提高应用程序的性能。

我采用了一种概念性的方法来解释代码分裂。如果您需要更多技术支持，请查看官方的React文档。他们在技术上解释这个概念方面做得不错。
Another approach is component based code splitting. In this method, if a component is currently not displayed to the user, it’s code may be delayed from being sent to the user.

Whichever method you stick to, it is important to understand the trade-offs and not degrade the user experience of your application.

Code splitting is great, and it can improve your application’s performance.

I have taken a conceptual approach to explain code splitting. If you want more technical grounds, please have a look at the official [React docs](https://reactjs.org/docs/code-splitting.html). They do a decent job at explaining the concept technically.

### 结论

现在，您已经有了一个像样的清单来跟踪和修复反应应用中的常见性能问题。去构建一些快速应用程序！
Now you’ve got a decent checklist for tracking and fixing common performance issues in react apps. Go build some fast apps!

![](/images/JS_Weekly/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/minimal-rocket-2xA-1.png)

[https://logrocket.com/signup/](https://logrocket.com/signup/)

## 资源
* 原文 https://logrocket-blog.ghost.io/death-by-a-thousand-cuts-a-checklist-for-eliminating-common-react-performance-issues/
* Github https://github.com/ohansemmanuel/Cardie-performace
