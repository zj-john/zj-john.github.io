---
title: 再见JS，Webassembly你好
categories:
  - notes
tags:
  - JavaScript Weekly
  - javascript
  - webassembly
  - 2018.10.05
toc: true
date: 2018-10-10 11:16:35
---

**一种新的Web开发形式开始出现，它有望提供JavaScript的替代方案：这就是WebAssembly。**

<!-- more -->

Web开发一直是JavaScript开发的代名词。从诞生直到现在。现在，一种新的Web开发形式开始出现，它有望提供JavaScript的替代方案。作为一名拥有15年Web开发经验的软件开发人员，这一新方向引起了我的兴趣。


[WebAssembly](https://webassembly.org/)（Wasm）是Web浏览器的二进制指令格式，旨在为C＃等高级语言提供编译。最近，微软开始尝试使用WebAssembly，[使用Mono引擎把.NET引入浏览器](https://www.mono-project.com/news/2017/08/09/hello-webassembly/)。Mono提供了在WebAssembly上运行的.NET库（.dll）的管道。运行在Mono之上的是[Blazor](https://blazor.net/)，一个基于.NET的单页Web应用程序框架，通过Mono的WebAssembly引擎运行在在浏览器中。WebAssembly-Mono-Blazor技术栈使得具有完全.NET技术栈开发能力的Web开发人员能够在不使用JavaScript或浏览器插件的情况下开发完整web应用的潜力。

![WA](/images/JS_Weekly/goodbye-javascript-hello-webassembly/wac593c40001584d1f973f7d6624e330b1.jpg)

引入这个新概念会立即带来问题，这是理所当然的。

WebAssembly提供了哪些内容是JavaScript或TypeScript没有的呢？
What Does WebAssembly Provide That JavaScript or TypeScript Doesn’t?
--------------------------------------------------------------------
我的答案伴随着大量的偏见和意见，我觉得应该并非所有的开发人员，项目和工具都是一样的。对我来说答案很明确，简短的回答是“选择”。开放超越JavaScript的Web开发意味着选择和自由选择不仅是JavaScript或.NET，而是更广泛的选择。更确切地说，就我个人而言，我可以选择使用我已在其他地方使用的工具和语言来开发Web应用程序。

### npm和WebPack

特别的，对.NET开发敞开WEB开发大门的好处之一，是我们把npm和WebPack变为了可选答案。作为很长一段时间的.NET开发人员，对于使用NuGet（包管理器）和MSBuild我很开心。对我来说，这些技术问题更少，更熟悉，而且效率更高。虽然没有什么是完美的，但我与NuGet和MSBuild的关系大多是积极的。
One of the benefits of opening up the web to .NET in particular is that we now have alternatives to npm and WebPack. As a long time .NET developer, I’m greeting NuGet (package manager) and MSBuild with excitement. For me, these technologies are less problematic, more familiar, and far more productive. While nothing is ever perfect, my relationship with NuGet and MSBuild has been mostly positive.  

![npm](/images/JS_Weekly/goodbye-javascript-hello-webassembly/npma2377ad1f06f41d0b6f70de939cd5e1c.png)

起初，这可能会给人的印象是npm和Webpack有点不好，而且我主张放弃这些工具，但事实恰恰相反。npm和WebPack是很棒的工具，它们可能会存在很长一段时间。如果您的JavaScript工具适合您和您创建的应用程序，那么这是一件很棒的事情。拥有悠久的网络经验，我了解为什么npm和WebPack存在并欣赏他们拥有和将要完成的事情。
At first this may come with the impression that npm and Webpack are somehow bad, and that I’m advocating to abandon those tools, but the opposite holds true. npm and WebPack are great tools and they will likely be around for quite some time. If your JavaScript tools work well for you and the apps you create then this is a wonderful thing. Having a long history of experience with the web, I have an understanding of why npm and WebPack exist and appreciation for what they have and will accomplish.


### 减少学习曲线

Blazor让我感到震惊的一件事是它的使用感觉非常简单。为了不偏不倚，我承认它不是完整的功能，还没有大规模测试。Blazor将Razor（UI）的易用性与其他.NET Core概念结合在一起，例如：依赖注入，配置和路由。它借用了流行的JavaScript框架（如Angular和React）中的最佳模式，同时利用了Razor模板，并提供了与其他.NET约定的奇偶校验。这些功能组合允许以前所未有的方式重用技能。对于使用一种语言和熟悉完整JavaScript技术栈的Node开发人员来说，情况也是如此。
One thing that shocked me about Blazor is how genuinely simple it feels to use. In an attempt to be unbiased, I’ll admit that it’s not feature complete and it’s yet to be tested at scale. Blazor combines the ease of Razor (UI) with other .NET Core concepts like: dependency injection, configuration, and routing. It has borrowed the best patterns from popular JavaScript frameworks like Angular and React while leveraging Razor templates, and provided parity with other .NET conventions. This combination of features allows for the reuse of skills in a way that was unavailable before. The same could be said for Node developers who use one language and familiar concepts in full stack JavaScript apps.

你仍需要JavaScript
-------------------------

使用WebAssembly并不意味着可以避免使用JavaScript。WebAssembly当前必须由JavaScript加载和编译。（是的，我可以听到记录划痕。）虽然将来计划允许像ES6模块一样加载WebAssembly模块，但JavaScript可以引导WebAssembly。JavaScript的必要性也不止于此。WebAssembly无权访问任何平台API。为了访问平台API，需要JavaScript。
Using WebAssembly doesn’t mean that JavaScript can be avoided. WebAssembly must currently be loaded and compiled by JavaScript. (Yes, I can hear the record-scratch.) While there are future plans to allow WebAssembly modules to be loaded just like ES6 modules, JavaScript is there to bootstrap WebAssembly. The necessity for JavaScript doesn’t stop there either. WebAssembly doesn’t have access to any platform APIs. In order to access platform APIs JavaScript is required.

### Blazor Interop

WebAssembly应用程序可以调用JavaScript，为纯WebAssembly无法访问的API提供迁移路径。此功能也用于Blazor框架。由于Blazor是新的和实验性的，[Blazor interop](https://blog.logrocket.com/working-with-the-blazor-javascript-interop-3c2a8d0eb56c) 允许开发人员在WebAssembly本身存在缺陷时，或者因为Blazor框架尚未成熟时依赖JavaScript。此外，interop是许多开发人员将在C＃中使用的抽象层，他们不必担心底层技术仍在执行JavaScript代码。随着WebAssembly的成熟，随着时间的推移，对抽象的需求将会减少。
WebAssembly applications can make calls to JavaScript, providing a migration path for APIs that are beyond the reach of pure WebAssembly. This feature is used in the Blazor framework as well. Because Blazor is new and experimental, the [Blazor interop](https://blog.logrocket.com/working-with-the-blazor-javascript-interop-3c2a8d0eb56c) allows developers to fall back on JavaScript when there are shortcomings of WebAssembly itself, or because the Blazor framework is not yet mature. In addition, the interop is an abstraction layer that many developers will work with in C#, and they will not need to worry that underlying technology is still executing JavaScript code. Over time the need for abstractions will decrease as WebAssembly matures.  

![blazor-block-diagram](/images/JS_Weekly/goodbye-javascript-hello-webassembly/blazor-block-diagram.jpg)

再见不是永远的
Goodbye Isn’t Forever
---------------------

Progress对使用[Angular，React，Vue和jQuery](https://www.telerik.com/kendo-ui)的JavaScript进行了大量投资。Progress旗下最令人兴奋的开源框架之一是[NativeScript](https://www.nativescript.org/)。NativeScript是一个使用JavaScript为iOS和Android创建原生移动应用程序的框架。NativeScript让我想起了WebAssembly，它为开发人员创建选择的方式。借助NativeScript，JavaScript开发人员可以重用其现有技能进入移动开发领域，从而使其在员工队伍中更具价值。NativeScript的目标是赋予开发人员权力，而不是降低Swift，Objective-C或Java的价值。
Progress has huge investments in JavaScript with [Angular, React, Vue, and jQuery](https://www.telerik.com/kendo-ui). One of the most exciting open source frameworks under the Progress umbrella is [NativeScript](https://www.nativescript.org/). NativeScript is a framework for creating native mobile applications for iOS and Android using JavaScript. NativeScript reminds me of WebAssembly in the way that it creates choices for developers. With NativeScript JavaScript developers can reuse their existing skills to enter the mobile development space, thus making them more valuable in the workforce. NativeScript’s goal is to empower developers, not diminish the value of Swift, Objective-C, or Java.

我觉得WebAssembly也有类似的目标。实际上，该目标在WebAssembly[官方文档](https://webassembly.org/docs/faq/)中说明。
I feel that WebAssembly shares a similar goal. In fact, that goal is stated on the [official WebAssembly documentation](https://webassembly.org/docs/faq/).




> **WebAssembly是否试图取代JavaScript？**  
>没有！WebAssembly旨在作为JavaScript的补充而非替代。随着时间的推移，WebAssembly将允许将许多语言编译到Web上，而JavaScript具有令人难以置信的动力，并且仍将是Web的单一特权（如上所述）动态语言。此外，预计JavaScript和WebAssembly将在许多配置中一起使用......
> No! WebAssembly is designed to be a complement to, not replacement of, JavaScript. While WebAssembly will, over time, allow many languages to be compiled to the Web, JavaScript has an incredible amount of momentum and will remain the single, privileged (as described above) dynamic language of the Web. Furthermore, it is expected that JavaScript and WebAssembly will be used together in a number of configurations…

向前进
Moving Forward
--------------

如果你对使用JavaScript替代方案开发Web有兴趣，那么WebAssembly和像基于ASP.NET内核的Blazor这样的框架值得投入[一些](https://www.telerik.com/blogs/blazedown-experiment-with-markdown-and-blazor) [时间](https://www.telerik.com/blogs/a-breakdown-of-blazor-project-types)。这些仍然是WebAssembly和基于WebAssembly的技术的早期阶段，但是扩大生态系统的承诺引起了我的注意。作为Web开发的忠实粉丝，我希望看到它向前发展并扩展应用程序如何为平台编写的想法。利用多年的.NET经验来构建应用程序以提高我的工作效率的前景令人兴奋。此外，我已经为JavaScript技能打下了坚实的基础，我每天都在不断成长。通过这种多样性，技能可以作为工程师解决问题的视角和独特方式。

If developing for the web using JavaScript alternatives interests you then WebAssembly and frameworks like ASP.NET Core’s Blazor are worth investing [some](https://www.telerik.com/blogs/blazedown-experiment-with-markdown-and-blazor) [time](https://www.telerik.com/blogs/a-breakdown-of-blazor-project-types) in. These are still early days for WebAssembly and WebAssembly based technologies, but the promise of a widening ecosystem has gotten my attention. As a huge fan of web development I want to see it move forward and expand ideas of how apps are written for the platform. The prospect of leaning on years of .NET experience to build apps in a way that makes me more productive is exciting to say the least. In addition, I have built a solid foundation of JavaScript skills as well, that I continue to grow each day. With this variety skills comes perspective and unique ways to solve problems as an engineer.

WebAssembly会让您感兴趣吗？你打算测试Blazor吗？或者您是像Ruby或Python这样的背景的开发人员，想要在管道中使用WebAssembly？在下面的评论中分享您的想法。
Is WebAssembly something that interests you? Do you plan on testing out Blazor? Or are you a developer from a background like Ruby or Python that would like to use WebAssembly in your pipeline? Share you thoughts in the comments below.


## 链接
* 原文：https://www.telerik.com/blogs/goodbye-javascript-hello-webassembly
