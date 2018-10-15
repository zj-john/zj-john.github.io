---
title: 评估任何新JavaScript库时需要考虑的12件事
categories:
  - notes
tags:
  - JavaScript Weekly
toc: true
date: 2018-09-17 15:51:01
---


![](/images/JS_Weekly/the-12-things-you-need-to-consider-when-evaluating-any-new-javascript-library/1_PSo6PqzblBfox7FHlG_ITA.png)

> 您如何知道新技术是否值得投入时间？

对于今年的[JavaScript状态调查](http://stateofjs.com/)，我想深入挖掘一下，不仅知道人们使用了哪些工具和库，还知道他们使用它们的原因。

这意味着我必须找到一种方法将个人偏好转化为数据。经过一些研究，我提出了12分制，涵盖了挑选和使用任何技术的主要方面。

<!-- more -->

## 参加测验

为了让您更容易将12分制应用于任何库，我准备了一个快速测验，将引导您完成所有12个因素并给出最终建议：

#### ➡️[参加12因素测验](https://stateofjs.typeform.com/to/hTRAcc)

如果您不确定要评估什么，只需在您熟悉的库（React，Vue，jQuery ......）上进行评估，看看它的得分情况如何！

[![](/images/JS_Weekly/the-12-things-you-need-to-consider-when-evaluating-any-new-javascript-library/MBboAejBMAO7hIShzw.png)](https://stateofjs.typeform.com/to/hTRAcc)

或者，您可以继续阅读以了解有关每个因素的更多信息，并了解如何应用这些因素。

## 注意：关于JavaScript调查的状态

就像我提到的那样，我最初开发这种得分制是为了获得[年度JavaScript状态调查](http://stateofjs.com/)的更精细数据。

![](/images/JS_Weekly/the-12-things-you-need-to-consider-when-evaluating-any-new-javascript-library/Nl0qT65eX1gc8Kd_HU6Pwg.png)

如果您想贡献并帮助确定JavaScript生态系统的最新趋势，请参加[调查]((http://stateofjs.com))！

现在回到12分制。

## 因素

这是完整列表：


1.  **🕹️特色**
2.  **🐞稳定性**
3.  **⚡表现**
4.  **🎁包装生态系统**
5.  **🌎社区**
6.  **👶学习曲线**
7.  **📖文档**
8.  **🔧工具**
9.  **🏛️跟踪记录**
10.  **👫团队**
11.  **⚖️兼容性**
12.  **📈动量**

我将解释每个因素的重要性，并为您提供一个评分方式，向您展示如何评估它。我们来看看清单吧！


### 🕹️特色

你选择任何技术的第一个原因可能就是它的作用。

但这里的关键问题是了解这个库的功能边界。React可能是目前最流行的前端库，但常见的抱怨是它做得还不够，比如它将路由和状态管理等内容留给第三方库，如React-Router和Redux。

事实上，这是React最大竞争对手Vue的一个重要吸引点。Vue通过为常见用例提供官方软件包，它可以提供更全面的解决方案并获得大量支持。

再说一次，边界太远（功能太多），你可能会得到一个臃肿，复杂的框架，试图成为每个人的唯一。

所以有时候，最简单的方法就是找需要的。像Lodash或Ramda这样的库可以让你用简洁的函数表达式替换乱糟糟的嵌套for循环，这足以使它们成为重要的工具。

同样，这一切都是为了寻找合适的平衡！

#### 评分系统

*   A：解锁之前无法实现的事情。
*   B：让你做与以前一样的事情，但是以更好的方式。
*   C：比现有解决方案少。

### 🐞稳定性

您可以拥有最优雅，功能全面的框架，但如果开发人员使用时每两分钟发生一次错误，则不会拥有太多用户。

因此，当前JavaScript生态系统中的许多工具都专注于添加稳定性和安全性。只看TypeScript和Flow的成功，甚至是Reason等语言。

在数据层方面，GraphQL的类型系统也有助于确保一切顺利运行。

#### 评分系统

*   A：更少的错误，问题变得更容易调试和解决。
*   B：采用该技术不会对软件的稳定性产生影响。
*   C：采用该技术的直接后果是出现了新的错误和问题。

### ⚡表现

如果你曾经训练过武术，你就会知道你可以拥有的最好的属性之一就是速度，而不是力量。

同样，如果您的应用需要15秒才能加载，那么世界上的所有功能都无济于事。到那个时候，用户已经关闭了标签，你甚至在它开始之前就已经失败了！

在JavaScript生态系统中，只看[Preact](https://preactjs.com/)可以看到一个关注速度的例子：它的API与React完全相同，因此它不会试图在功能强度上进行竞争。但是，与React相比，它的重量更轻，加载更快，可以节省宝贵的毫秒数并提高webapp的性能。

#### 评分系统

*   A：更轻的捆绑，更快的加载时间或其他性能改进。
*   B：采用该技术不会对您的软件性能产生影响。
*   C：采用这项技术可以显着降低您的应用程序速度。

### 🎁包装生态系统

在投资任何新技术之前，重要的是要看看围绕它开发的生态系统。

一个充满活力的软件包生态系统不仅可以节省大量时间，因为它可以让您捎带其他人的工作，但这也表明该技术已经达到了一定的成熟度水平。出于这个原因，维护良好的第三方软件包是开发人员长期采用技术的最佳标志之一。

#### 评分系统

*   A：生态系统对共同关注的问题有明确的解决方案; 第三方软件包维护良好且记录良好。
*   B：具有许多竞争新选项的萌芽包装生态系统。
*   C：没有包装生态系统可言，需要大量的手工工作。

### 🌎社区

另一个要考虑的因素是整个社区。遇到问题时，专用论坛或Slack渠道可以提供巨大的帮助。
[![](/images/JS_Weekly/the-12-things-you-need-to-consider-when-evaluating-any-new-javascript-library/bxUL0M4P-4wLqLdG9RKBsw.png)](https://spectrum.chat/)
[频谱](https://spectrum.chat/)是聊天室和传统论坛之间日益流行的中间立场

拥有Stack Overflow现有的存储库以查找它也很有帮助。当然，维护良好的GitHub问题页面是必须的！

#### 评分系统

*   A：论坛和/或聊天室（Slack / Discord / etc。）与日常活动，GitHub问题在一天内解决。</font>许多人回答了Stack Overflow的问题。
*   B：不经常活动的论坛和/或聊天室。
*   C：GitHub之外没有社区。

### 👶学习曲线

简单的学习曲线使开发人员更有可能为您的框架或库提供一个机会。人们很容易认为，如果一项技术真正具有破坏性，那么人们就会克服任何障碍，但通常情况并非如此。

一个密切相关（但有时相反）的概念是“采用”曲线。首次推出时，[Meteor](http://meteor.com/)非常易于使用（至少与现有替代品相比），但它要求您立即采用整个堆栈，这使得现有项目很难实施。

React也以其粗略的学习曲线而闻名：对于用于分离HTML和JavaScript的开发人员来说，不得不使用JSX可能很难。另一方面，Vue使得开始更容易，而不必重新思考您对前端编码的思考方式。

#### 评分系统

*   A：可以在一天内开始。
*   B：在提高生产率之前需要大约一周的时间。
*   C：学习基础知识需要一周多的时间。

### 📖文档

简单学习曲线的一个重要部分就是拥有出色的文档。这比听起来更难实现，因为撰写文档的人通常是经验最丰富的人; 这意味着他们也离开了新的开发者体验。

因此，编写好的文档需要忘记你所知道的一秒钟，并让自己置身于发现你的技术的人之中。
[![Vue.js的文档既精心设计又精心编写](/images/JS_Weekly/the-12-things-you-need-to-consider-when-evaluating-any-new-javascript-library/1_tCx_c05d4dXANZ5AubgrTg.png)](https://vuejs.org/v2/guide/)

它还需要预测常见问题，了解用户的心理模型，最重要的是在代码库发生变化时保持最新状态！所有这些都需要宝贵的时间远离实际编码......

鉴于所有这些因素，您可以理解为什么好的文档是一种罕见且有价值的东西！

#### 评分系统

*   A：专用文档站点，截屏视频，示例项目，教程，API文档和评论良好的代码。
*   B：基本自述文件和API文档。
*   C：非常简洁自述，了解如何使用库的唯一方法是查看其代码。

### 🔧工具

就像文档一样，工具是这些事情中的一个，可能看起来像是一些维护者的次要分心，但实际上对于任何技术的普及和成功至关重要。
[![仅Redux的DevTools值得考虑](/images/JS_Weekly/the-12-things-you-need-to-consider-when-evaluating-any-new-javascript-library/1_PsiEdZb0tN2cBeuUj1SqdA.png)](https://github.com/zalmoxisus/redux-devtools-extension)

我相信Redux成功背后的一个重要原因是其令人惊叹的Devtools浏览器扩展，它允许您以非常用户友好的方式可视化Redux存储和操作。同样，VS Code的强大TypeScript支持也为它的采用创造了奇迹。

#### 评分系统

*   A：两个或多个：浏览器扩展，文本编辑器扩展，CLI实用程序，专用的第三方SaaS服务。
*   B：其中之一：浏览器扩展，文本编辑器扩展，CLI实用程序，专用的第三方SaaS服务。
*   C：没有外部工具。

### 🏛️跟踪记录

在一天结束的时候，即使是最优雅，记录最好的图书馆也会很容易被解雇，因为如果它只存在了六个月，那就不过是昙花一现了。

我们都可以讲述采用“下一件大事”的故事，只是在事情开始恶化的时候回到好的旧Rails / PHP / \*插入试用过的技术\*。

[![快递：即使经过这么多年，仍然是一个有力的竞争者](/images/JS_Weekly/the-12-things-you-need-to-consider-when-evaluating-any-new-javascript-library/1_6fazzlZ9zr9r4izYCSi3yg.png)](https://expressjs.com/)

出于这个原因，没有任何东西可以击败坚实的记录。</font>Express是其中一个例子：它最初是在2010年发布的，但仍然被认为是默认的Node.js服务器框架，尽管JavaScript生态系统的发展速度很快。

#### 评分系统

*   A答：已经四年多了，收养了我的主要公司和知名的技术咨询公司。
*   B：已经存在了1-4年，被早期采用者和小规模咨询公司使用。
*   C：已经存在不到一年，还没有真正的采用。

### 👫团队

并非所有项目都有现有的跟踪记录。当图书馆是全新的，你如何判断它的潜力？一种可靠的方式来看看它背后的人。

当React第一次出现时，除了Facebook背后的事实是一个很大的争论，至少要尝试一下。然后Facebook继续发布Relay和GraphQL，表明React的成功不是侥幸！
[![谷歌开源：超过2000个项目，涵盖桌面，移动等。](/images/JS_Weekly/the-12-things-you-need-to-consider-when-evaluating-any-new-javascript-library/1_bK-lNz_1QBNbDakIEAD4-A.png)](https://opensource.google.com/)

大公司也有更多的投资资源：即使发布了更新的，不兼容的版本，谷歌也能够继续保持原有的Angular.js。

当然，这并不意味着单独的维护者也无法创造重大创新。这毕竟是Vue.js的诞生，更不用说99％的开源软件了。

#### 评分系统

*   A：由一家拥有专门的开源团队的大公司维护。
*   B：由一个中等规模的工程师团队维护，拥有坚实的个人记录。
*   C：孤独的维护者独立工作。

### ⚖️ 兼容性

采用尖端库的好处在于它们通常发展得非常快。可悲的是，这也可能是一个重大的缺点！

快速改进率也可能意味着频繁的突破性变化，因为新的最佳实践取代了旧的模式，让早期采用者支付重构成本。

当他们决定在版本3和版本4之间完全改变他们的API时，React Router产生了很多抱怨。当他们从Angular.js切换到新的“只是Angular”时，Angular也是如此。

当您刚刚开始一个新项目时，频繁更新很有趣也很令人兴奋，但是一旦您的应用程序启动并在生产中运行，您最不希望的是每次新版本的库来时都需要花费数周的重构和调试出。

#### 评分系统

*   A：更新大多是向后兼容的，弃用是通过警告处理的，不兼容的旧版本可以维护两年或更长时间。
*   B：确实发生了突破性的变化，但有很好的记录，并逐步推出。
*   C：如果没有适当的指导，经常需要进行重大更新。

### 📈动量

最后但同样重要的是，势头。换句话说，炒作。

炒作通常被认为是一件坏事（“不要成为炒作的受害者”），作为风格超过实质的指标。但并非总是如此。

有了足够的动力，一个新的软件项目可以吸引更多的用户和更多的贡献者，这意味着可以更快地找到和修复错误，一个包生态系统可以开发，每个人最终都会变得更好。
[![JavaScript Rising Stars，我们的项目绘制了流行的JavaScript库的增长](/images/JS_Weekly/the-12-things-you-need-to-consider-when-evaluating-any-new-javascript-library/1_S9myzN7xp3KqeCIXbmG4Qg.png)](https://risingstars.js.org/2017/en/)

但是，是的，还有硬币的另一面：过早炒作太多可能会让潜在用户面临一个充满问题的未完成版本，并将其彻底解决。就像他们说的那样，你只有一次机会给人留下第一印象。

#### 评分系统

*   A：超过9000的炒作水平：黑客新闻的顶部，成千上万的GitHub明星，在主要会议上进行会谈。
*   B：最初推出的一些兴趣，数百名GitHub明星。
*   C：孤独的开发商在默默无闻中辛勤劳作。有一天我会告诉他们！我会告诉他们所有!!

### 更新：更多因素

你们中的一些人提出了一些值得关注的重要因素。要考虑潜在版本2.0的规模！

*   可扩展性：该技术对大型项目的效果如何。
*   采用：目前还有谁在使用该技术？
*   兼容性：该技术与其他现有技术的**兼容性**如何？
*   解耦：如果您想停止使用它，迁移出技术有多容易？

### 案例研究：Apollo客户

让我们将我们的评分系统应用到一个真实的，真实的图书馆：[Apollo Client](https://github.com/apollographql/apollo-client)。
[![阿波罗客户](/images/JS_Weekly/the-12-things-you-need-to-consider-when-evaluating-any-new-javascript-library/1_oVDdTyOdg9o6Vde4RigJfA.png)](https://www.apollographql.com/client/)

Apollo是一个GraphQL客户端，换句话说，它是一个库，它将查询GraphQL端点并为您加载客户端上的数据。它还处理诸如缓存之类的事情，确保数据不会重复，并将所述数据发送到您选择的前端库。

让我们看看它在我们的评分系统中的作用！

#### 🕹️特点：B

Apollo为您提供了更好的查询数据的方法，因此它更多地是对现有工具的逐步改进。

#### 🐞稳定性：A

采用Apollo和GraphQL确实可以更轻松地推断您的数据并追踪问题。

#### ⚡表现：B

Apollo确实包含了优化数据加载的工具，但总体上不应该对应用程序的性能产生巨大影响。

#### 🎁包装生态系统：A

Apollo支持称为[链接的](https://www.apollographql.com/docs/link/#linkslist)包</font>，以启用额外的功能。

#### 🌎社区：B

Apollo确实有一个非常活跃的Slack聊天室，但根据我的经验，问题有时可能无法回答，很难得到繁忙的核心团队成员的回复。

#### 👶学习曲线：B

学习阿波罗的所有细微差别实际上可能是一个挑战，特别是如果你正在学习同时使用GraphQL。

#### 📖文件：A

为多个前端框架以及示例代码库提供了良好，维护良好的文档。

#### 🔧工具：A

浏览器扩展和专用[度量平台](https://www.apollographql.com/engine/)。

#### 🏛️跟踪记录：B

Apollo本身仍然相当新，但一般来说GraphQL空间也是如此。

#### 👫团队：A

非常称职和资金充足的团队，具有启动其他开源项目（[Meteor](http://meteor.com/)）的经验。

#### ⚖️稳定性：B

打破从v1到v2的更新，但总体上良好的稳定性和向后兼容性。

#### 📈动量：B

阿波罗可能还不是一个家喻户晓的名字，但它仍然是其利基的主导者，尽管雷莱的先声夺人。


……


#### 总体成绩：A🥇

在最多36分中得到29分，阿波罗最终表现得非常好！即使总会有需要改进的地方，也很容易理解为什么许多需要可靠方法处理GraphQL数据的团队才会采用它。

### 其他方法

NPMS的人们已经实施了[类似的评级系统](https://npms.io/about)，通过查看GitHub和NPM数据实现了自动化。这使得他们的得分不那么主观，但另一方面却没有涵盖文档或社区等内容。

在原始数据方面，您还可以通过NPM趋势获得一些很酷的统计数据：
[![NPM趋势](/images/JS_Weekly/the-12-things-you-need-to-consider-when-evaluating-any-new-javascript-library/1_zQ374--VdS_-EVXTkmB-Gg.png)](https://www.npmtrends.com/)

并详细了解最佳JS上目前流行的库：
[![最好的JS](/images/JS_Weekly/the-12-things-you-need-to-consider-when-evaluating-any-new-javascript-library/1_FlMNol5iz7oyNncFaz250A.png)](https://bestofjs.org/)

当然，[去年的JS州调查结果](https://2017.stateofjs.com/)总是如此</font>：
[![2017年JavaScript状态调查结果](/images/JS_Weekly/the-12-things-you-need-to-consider-when-evaluating-any-new-javascript-library/1_l3q445UqLtf5pF8g6hkqag.png)](https://2017.stateofjs.com/)

你怎么样，你通常如何评估图书馆？发表评论让我知道！

### 结论

这个量表绝不是图书馆价值的绝对衡量标准。毕竟这将主要是主观的，并且很大程度上取决于您的项目和您的需求。

不过，我们希望它可以作为一个有用的起点。如果没有别的，它可以作为一个清单，以确保你没有忽略任何重要的事情，然后再进入未来的大跳跃！

## 资源
* 原文 https://medium.freecodecamp.org/the-12-things-you-need-to-consider-when-evaluating-any-new-javascript-library-3908c4ed3f49
