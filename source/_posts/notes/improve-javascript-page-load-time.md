---
title: 如何减少JavaScript对页面加载时间的影响
categories:
  - notes
tags:
  - JavaScript Weekly
  - javascript
  - load time
  - 2018.10.26
toc: true
date: 2018-10-30 10:52:02
---

![dark blue background with light blue balloon weighed down like your site would be weighed down with code](/images/JS_Weekly/improve-javascript-page-load-time/18_10_04_keepitfast_og_01@2x.jpg)

当我们为新客户，尤其是工程师客户宣传时，我们经常会遇到一个问题：[GoSquared](https://www.gosquared.com)的JS代码将对网站性能有多大影响。

把GoSquared的一小段JS代码放到您的网页上，它就能为您提供[分析](https://www.gosquared.com/analytics/)，[发现潜在顾客](https://www.gosquared.com/assistant/)，[实时聊天](https://www.gosquared.com/live-chat/)等功能。  
我们将所有这些功能集中在一个小代码段中，以使我们的客户能更轻松的使用它们。但是这可能会因为增加了额外的大小而使您的网站速度变慢，为此我们付出了很多努力来使我们的脚本尽可能快速轻量。

本文是我们在如何减少JS对页面加载时间影响的过程中学到的经验教训，您应该充分关注这些内容并将其应用到其他地方。

<!-- more -->

## 我们为什么要关注JavaScript的影响？
![dark blue background with pale blue blurred arrow and JS in large letters](/images/JS_Weekly/improve-javascript-page-load-time/18_10_04_keepitfast_07@2x.jpg)

网络变得越来越复杂，网站和Web应用程序的体验越来越丰富。随着越来越多的JS框架的兴起，构建这些丰富的体验比以往任何时候都更容易。

但这种丰富性和复杂性需要付出代价。提供这些体验需要添加额外的资源（脚本，样式表，图像，视频），它们中的每一个都会在用户的设备（CPU，GPU，内存，网络等）上消耗一定量的资源。

所有这些加起来构成了快速，友好与更慢，更令人沮丧体验之间的差异。  
由于页面加载速度慢是访问者放弃网站的主要原因之一，因此关注JS的影响非常重要。

确切地说，各种资源对最终用户的整体体验有多大差异取决于许多因素，比如不同类型的资源以不同的方式消耗不同的硬件，不同用户的设备性能也大不相同。

### JavaScript很贵

尽管如此，有一件事是清楚的：每字节都会消耗对应大小的流量。[JavaScript是迄今为止最昂贵的资源，特别是对于低端和功率不足的设备](https://medium.com/@addyosmani/the-cost-of-javascript-in-2018-7d8950fbb5d4) 。它不仅消耗网络资源来下载文件，而且解析，编译和执行这些脚本都消耗内存和CPU，这些在移动设备上可能都特别短缺。

当您在您的网站或网络应用程序上安装我们的代码时，会添加额外的JavaScript消耗，因此，对我们来说，将用户体验的成本降至最低限度非常重要。简而言之，我们确保我们的脚本尽可能小而精简。

这并不像说个口号那么简单，而是要尽所有可能来削减字节，而不管其他任何成本。而且，这种优化更多的只针对我们的成本节约，而不是降低用户的体验。

这是我们在持续优化的工作，以保证JavaScript包足够小，不仅对包含它的任何页面的整体性能影响最小，也对我们的开发过程影响最小。

## 快速获胜：缩小，压缩，分发

![dark blue background with COMPRESS in pale blue in the middle](/images/JS_Weekly/improve-javascript-page-load-time/18_10_04_keepitfast_03@2x.jpg)

互联网上的带宽不是无限的，而且光速意味着每当在很大的地理距离上发送数据时，延迟是不可避免的。

在减少网络消耗方面有一些快速的方法，且不必改变你的代码：

*   利用像Uglify或Closure这样的编译器 **缩减** 你的代码。这样既减少代码的大小，又不会缩减功能。
*   使用HTTP传输编码（如gzip或brotli）**压缩** 传输中的资源。大多数服务器或CDN（见下文）都支持设置一键启用此功能。
*   使用内容交付网络（CDN）分发您的静态资源，它支持从非常靠近访问者的物理位置为其提供服务。这可以减少下载资源时产生的延迟。

以上这些都是广泛使用，并且几乎是行业标准的实践。但重要的是去用它们，因为它们可以对用户的体验产生巨大影响。  
但它们在开发过程中也很容易忘记，因为如果在本地或在办公室中使用高速有线连接进行开发，它带来的网络效果不明显。

但这只是故事的第一部分。浏览器下载脚本后，还有几个步骤，这些步骤仍然有助于整体资源使用和页面速度。

## 解析，编译，执行
大多数现代浏览器的JavaScript引擎实际运行脚本的方式大致可以简化为三个主要阶段：解析，编译和执行。

**解析** 是读取浏览器接收的脚本内容并生成脚本结构在浏览器内部中展示的过程。
**Parsing** is the process of reading the bytes of a script received by the browser and generating an internal representation of a script’s structure.

**编译** 采用内部表示并使用它来生成可由处理器直接运行的机器代码，执行是该代码的实际运行。
**Compilation** is taking that internal representation and using it to generate machine code that can be directly run by the processor, and execution is the actual running of that code.

（注意：这实际上是对实际情况的过度简化 - 实际上每个浏览器的JavaScript引擎都有自己的多步骤管道 - Chrome的V8 具有Turbofan 和Ignition，Firefox具有IonMonkey - 并且有许多令人难以置信的聪明的计算机科学正在进行中。总体要点大致相同，但）
(Note: this is a vast oversimplification of what actually happens – in reality each browser’s JavaScript engine has its own multi-step pipeline – Chrome’s V8 has [Turbofan and Ignition](https://v8.dev/blog/launching-ignition-and-turbofan), Firefox has [IonMonkey](https://wiki.mozilla.org/IonMonkey/Overview) – and there’s a lot of incredibly clever computer science going on. The overall gist is roughly the same, though)

这些阶段中的每一个都以略微不同的方式消耗处理器和内存资源，但同时优化所有三个阶段的最可靠方法很简单：减少代码。
Each of these phases consumes processor and memory resources in slightly different ways, but the most sure-fire way of simultaneously optimising for all three is simple: ship less code.

![dark blue background with pale blue boat and ship less code written across ](/images/JS_Weekly/improve-javascript-page-load-time/18_10_04_keepitfast_02@2x.jpg)

这与简单地通过线路向浏览器发送更少的字节不同。传输压缩没有区别（浏览器已经收到了所有字节），甚至代码缩小也可以忽略不计 - 一旦浏览器解析了代码，所有缩小操作（例如删除注释和重命名变量）几乎没有区别。
This isn’t the same thing as simply sending fewer bytes over the wire to the browser. Transmission compression makes no difference (the browser has already received all the bytes), and even code minification is negligible – once the browser has parsed the code, all minification operations such as removing comments and renaming variables make practically no difference.

在这一点上，更多的是关于代码的整体复杂性和结构产生差异 - 一般来说，代码中不同函数，语句和其他声明的数量和排列。
At this point it’s more about the overall complexity and structure of the code that makes the difference – broadly speaking, the number and arrangement of different functions, statements, and other declarations in the code.

根据用户的设备，这是对用户的影响最大的部分。就像带宽一样，重要的是要记住，不是每个人都总是使用超高速的办公室互联网连接，因此同样重要的是要意识到并非每个人都使用高端MacBook Pro作为他们的桌面设备和最新的iPhone - 这去。设备功率的变化非常大，特别是在移动设备上。在高端iPhone上解析，编译和评估的时间可能只需要一秒钟，在“普通”移动设备上可能需要4-5倍，而在低功耗设备上则需要10-20倍。
This is the part where the impact to the user varies most depending on the user’s device. Just as with bandwidth it’s important to remember that not everybody is always on a super-fast office internet connection, so it’s also important to realise that not everybody is using a high-end MacBook Pro as their desktop device and the latest iPhone when on-the-go. The variability in device power is incredibly wide, especially on mobile. What might take just a second to parse, compile, and evaluate on the top-end iPhone might take 4-5 times longer on an “average” mobile device, and 10-20 times longer on a low-power device.

迫使用户在他们的浏览器通过兆字节的JavaScript进行搅拌时等待几秒钟是一种糟糕的体验。目前，上述所有阶段都在浏览器的主CPU线程上运行，这意味着当它们发生时，网页本身实际上是无响应的，因此对于像GoSquared Assistant这样的第三方小部件保持这样的状态非常重要。中断到绝对最低限度。
Forcing your users to wait several seconds while their browser churns through megabytes of JavaScript is a terrible experience. Currently, all of the above phases run on the browser’s main CPU thread, which means that while they’re happening, the web page itself is effectively non-responsive, so it’s incredibly important for a third-party widget like GoSquared Assistant to keep such interruption to an absolute minimum.

因此，考虑到所有这些，您如何才能真正减少代码大小？我们如何在GoSquared这里做到这一点？
So with all that in mind, how can you actually go about reducing code size? And how do we do it here at GoSquared?

## JS库的权衡
![dark blue background with a pale blue shelf of books with different engineering phrases on the spines](/images/JS_Weekly/improve-javascript-page-load-time/18_10_04_keepitfast_04@2x.jpg)

JavaScript生态系统近年来爆炸式增长。随着打包工具，像[Webpack](https://webpack.js.org/)和[Rollup](https://rollupjs.org/)的出现，以及包管理工具[npm](https://www.npmjs.com)的发展，现在比以往更容易在您的JavaScript包中引入第三方模块。

其中一些第三方模块是框架，一些是界面组件，还有些是有用的工具包，可以让您直接使用复杂的逻辑而无需自己实现。  
你可能因为不同的动机而选择使用它们，但是在你使用工具包之前，都应该按照相同的标准评估它们。  
每当我们想要添加新的依赖时，我们都会试着问自己：

*   我是否需要添加新库？
*   我需要添加这个库吗？

第一个问题是关于用第三方插件还是自己写的选择问题，这是一个非常主观和有争议的问题。与许多事情一样，真正的答案是“视情况而定”。在每种情况下，您都应权衡所涉及的收益和成本：

*   与自己写（和测试）代码相比，第三方插件可以节省多少开发时间？
*   与自己写相比，采用第三方插件会为整体打包增加多少额外的大小？
*   第三方插件是否包含对您不会使用的大量输入和用例的支持？Does the third-party version include support for a wide set of inputs and use-cases that you simply won’t be using?
*   这些问题的答案对彼此有多大关系？How much do the answers to these questions matter in relation to each other?

以上问题的答案完全取决于您要开发的内容和最后呈现的方式。

在GoSquared，我们倾向于更倾向于编写自己的代码而不是使用第三方代码，如果它将在最终捆绑中节省大小而不牺牲我们自己的生产力。但那是因为我们的代码被嵌入到我们客户的数千个网站上 - 如果它的膨胀或缓慢，那么这会让我们的客户看起来很糟糕，而不是我们。我们希望确保任何选择添加GoSquared的网站始终认为它是一种帮助，而不是一种障碍。
At GoSquared, we tend to err slightly more towards writing our own code than using third-party code, if it will save size in the final bundle without sacrificing our own productivity. But that’s because our code gets embedded on thousands of our customers’ sites – if it’s bloated or slow, then that makes our _customers_ look bad, rather than us. We want to make sure that any site which chooses to add GoSquared always feels it’s a help, never a hindrance.

## 做出最佳选择
上面的第二个问题是关于评估和选择第三方插件的。这是一个有趣的问题，因为JavaScript生态系统已经蓬勃到对于任何给定的需求，通常都有几个插件可以选择。这意味着您可以在多个选择间权衡。

在各种不同插件之间选择的标准与评估是否首先使用第三方插件的标准大致相同。

*   从开发的角度来看哪个插件更容易使用？
*   哪个在浏览器中有更好的性能？
*   哪个总体尺寸最小？

同样，这些问题的答案，以及你如何权衡，都是非常主观的。  
就GoSquared而言，我们更倾向于在尺寸和性能方面能在客户端表现更好的插件。

举例来说，我们是大球迷的阵营构建UI。我们通过React Native在我们的主要网络应用程序和iOS和Android应用程序中全面使用它。因此，对于助理，我们希望以类似的方式工作。但是，捆绑包大小更为重要，因此我们使用Preact，它是React的（仅仅）API兼容的插件，但占用空间相对较小。通过对我们的构建过程进行一些调整，我们能够编写与React完全相同的代码，但在最终的捆绑中使用Preact。
For example, we’re [big fans](https://twitter.com/floopily/status/660052105894469633) of [React](https://reactjs.org/) for building UI. We use it across the board in our main web app and in our iOS and Android apps via React Native. So for the Assistant, we wanted to work in a similar way. However, bundle size is much more important, so we instead use [Preact](https://preactjs.com/), which is a (just about) API-compatible drop-in for React, but with a comparably _tiny_ footprint. With a few tweaks to our build process, we’re able to write the exact same code we do for React, but with Preact in the final bundle.

有时它并不像将一个库换成另一个库那么简单; 通常会涉及一定程度的开发工作。您是否需要根据自己的情况评估开发工作是否对您有益。
Sometimes it’s not as simple as swapping one library out for another; often there’ll be some degree of development work involved. Whether that development effort is worth the benefit to you is something you’ll need to evaluate based on your own situation.

Bundlephobia是一个非常快速评估库潜在重量的好工具，它通过分析源代码来计算包含模块的粗略影响。唯一需要注意的是，它不能考虑你是包括整个图书馆还是
仅包括某些部分 - 不久之后就会更多。但是，作为一般指南使用它很棒。
A great tool for very quickly evaluating the potential weight of a library is [Bundlephobia](https://bundlephobia.com/), which calculates the rough effect of including a module by analysing its source code. The only caveat being that it can’t take into account whether you’re including the whole library or just some parts – more on that shortly. It’s great to use as a general guide, though.

## 如果可以，仅导入您需要的内容
评估第三方库时要记住的另一个重点是可分解性问题。
Another important point to bear in mind when evaluating third-party libraries is the matter of _decomposability_.

也就是说，如果一个库支持各种各样的用例，其中大部分都是你不会使用的，它是否以这样的方式编写，只允许你只提取你真正需要的那些部分？像Webpack或Rollup这样的捆绑器具有“树摇动”或“死代码消除”的概念，这是确保输出仅包括您实际使用的代码的不同方式。
That is, if a library supports a whole variety of use-cases, most of which you won’t be using, has it been written in such a way that allows you to pull in only those parts you actually need? Bundlers like Webpack or Rollup feature the idea of “tree-shaking” or “dead code elimination” which are different ways of ensuring the output only includes the code you actually use.

如果以正确的方式构建库，您应该只能包含所需的部分，即使库作为一个整体包含更广泛的功能集。
If a library is structured in the right way, you should be able to include only the parts you need, even if the library as a whole includes a much wider set of functionality.

这方面的一个主要例子是在JavaScript中操作日期。多年来，Moment.js因其多功能性和近乎完整的功能集而成为首选的库。几乎所有你可能想要做的日期（解析，操作，格式化），你可以用Moment做。
A prime example of this is in the field of manipulating Dates in JavaScript. Over the years, [Moment.js](https://momentjs.com/) emerged as the library of choice for its versatility and near-complete feature set. Pretty much everything you could possibly want to do with a date (parsing, manipulating, formatting), you can do with Moment.

但是，它主要是在现代捆绑器时代之前编写的，所以它不是用可分解性编写的 - 你在需要它的地方包含了脚本文件并获得了所有功能。然而，如今，有许多替代解决方案，要么使用JavaScript的内置功能，要么使用替代库，例如date-fns，这些都是考虑到树木抖动的。date-fns库不是Moment的简单替代品 - 从一个转换到另一个需要相当多的重构工作 - 但是我们在开发GoSquared Assistant时做出了一个决定，以避免大的非-decomposable库，如Moment。
However, it was written largely before the age of modern bundlers, so it wasn’t written with decomposability in mind – you included the script file where you needed it and got all the functionality. Nowadays, however, there are [plenty of alternative solutions](https://github.com/you-dont-need/You-Dont-Need-Momentjs), either using JavaScript’s built-in functionality, or alternative libraries such as [date-fns](https://date-fns.org/), which were written with tree-shaking in mind. The date-fns library isn’t a simple drop-in replacement for Moment – switching from one to the other requires a fair amount of refactoring work – but we made a decision early on in the development of the GoSquared Assistant to avoid large, non-decomposable libraries such as Moment.

这就是我们如何减少捆绑包中包含的第三方代码的影响。除了简单地编写我们自己的代码之外，我们还要做的最后一件事是保持我们的生产构建精益：
So that’s how we reduce the effect of third-party code included in a bundle. Besides simply writing less of our own code, there’s one last major thing we do to keep our production builds lean:

## 去除生产中不需要的一切
![dark blue background with pale blue scissors in the centre cutting through a dotted line](/images/JS_Weekly/improve-javascript-page-load-time/18_10_04_keepitfast_05@2x.jpg)

在开发过程中，您会在代码中写一些用于验证，调试或记录的逻辑，以确保代码符合您的预期（如果您还没这样做，那么可以去试试）。  
即使自己的代码中没有这些逻辑，您包含的第三方插件也可能包含，比如console.log语句，propTypes验证（如果使用React）等所有内容。

但是，在您构建面向访客的生产代码时，这些功能都没有任何用处，或者充其量只是很少使用。所以它应该被删除，而且最好由你的构建系统自动删除。

像React这样的大多数库都会用环境变量“控制”它们的开发模式逻辑，如下所示：
```js
    if (process.env.NODE_ENV === "production") {
      // fast version of code, no debugging
    } else {
      // slower version with better debugging
    }
```
您可能希望在自己的代码中执行类似的操作。一个好的捆绑器或缩小器将能够在这里检测并内联process.env.NODE_ENV的值（使用诸如rollup-plugin-replace或Webpack的EnvironmentPlugin之类的插件），并完全删除你不想要的代码部分包括。
You may want to do something similar in your own code. A good bundler or minifier will be able to detect and inline the value of process.env.NODE_ENV here (with plugins like [rollup-plugin-replace](https://github.com/rollup/rollup-plugin-replace) or Webpack’s [EnvironmentPlugin](https://webpack.js.org/plugins/environment-plugin/)), and fully strip out the parts of the code that you don’t want to include.

还有许多其他东西可能不应该包含在最终的生产包中，其中许多可以作为构建过程的一部分完全自动删除。例如，对于React propTypes，我们使用transform-react-remove-prop-types Babel插件从生产版本中删除所有开发模式验证和警告。
There are many other things that probably shouldn’t be included in the final production bundle, many of which can be completely automatically removed as part of the build process. For example, for React propTypes, we use the transform-react-remove-prop-types Babel plugin to remove all development-mode validation and warning from the production build.

## 最终效果

![seesaw on a dark blue background showing that gosquared assistant is lighter ](/images/JS_Weekly/improve-javascript-page-load-time/18_10_04_keepitfast_06@2x.jpg)

> 据我们所知，GoSquared Assistant是目前最精简，最快速的聊天小部件，大约10倍。To the best of our knowledge, GoSquared Assistant is the leanest and fastest chat widget available right now, by a factor of about 10×.
将所有这些技术放在一起用于GoSquared Assistant代码库，我们有：
Putting all these techniques together for the GoSquared Assistant codebase, we have:

*   缩小和压缩所有已部署的脚本 Minify & compress all deployed scripts
*   选择小而高效的库，如Preact而不是React，这是有意义的 Choose small and performant libraries such as Preact instead of React, where it makes sense
*   在生产模式下优化我们的构建设置，以删除仅与开发相关的所有代码 Optimise our build settings in production mode to remove all code that’s only relevant to development

### 降至39KB Getting down to 39KB
所有这一切都产生了一个捆绑包，它封装了18种语言的所有必要的JavaScript，第三方库，CSS和本地化字符串（在编写本文时）的测量值为112KB（gzip之后为39KB），并且解析和运行速度比其他任何其他语言快那里的现场聊天提供商。即使在相对低端的移动设备上，它对任何页面的影响也几乎都不可察觉。
All this results in a bundle that encapsulates all necessary JavaScript, third-party libraries, CSS and localisation strings for 18 languages that (at time of writing) measures in at 112KB (39KB after gzip), and which parses and runs faster than any other on-site chat provider out there. Even on a relatively low-end mobile device, the impact it has on any page it’s on is barely noticeable.
我们为GoSquared Assistant的表现所取得的成就感到非常自豪。据我们所知，它是目前最精简，最快速的聊天小部件，大约10倍，这对移动设备产生巨大影响。
We’re very proud of what we’ve managed to achieve in the performance of GoSquared Assistant. To the best of our knowledge, it’s the leanest and fastest chat widget available right now, by a factor of about 10×, which makes a huge difference on mobile devices.
显然，我们还可以做更多的事情，并且随着时间的推移我们将会添加更多功能，但您可以相信，每当我们添加任何新内容时，我们始终会将对性能的影响保持在我们客户的网站上。
Obviously, there’s still more we can do, and more functionality we’ll be adding over time, but you can trust that we’ll always be keeping the performance impact to our customers’ sites front-of-mind whenever we add anything new.

## 资源
* 原文 https://engineering.gosquared.com/improve-javascript-page-load-time
*   [The Cost of JavaScript in 2018](https://medium.com/@addyosmani/the-cost-of-javascript-in-2018-7d8950fbb5d4)
*   [The “Developer Experience” Bait-and-Switch](https://infrequently.org/2018/09/the-developer-experience-bait-and-switch/)
*   [The 12 Things You Need to Consider When Evaluating Any New JavaScript Library](https://medium.freecodecamp.org/the-12-things-you-need-to-consider-when-evaluating-any-new-javascript-library-3908c4ed3f49)
