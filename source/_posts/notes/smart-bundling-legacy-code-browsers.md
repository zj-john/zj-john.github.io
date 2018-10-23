---
title: 智能打包，如何仅为旧版浏览器提供旧版代码
categories:
  - browsers
  - performance
  - javascript
  - 2018.10.19
tags:
  - JavaScript Weekly
toc: true
date: 2018-10-23 11:33:00
---
虽然“有效打包资源”这个话题在网上一直很火，但不管如何打包，我们最终向不同用户提供的前端资源仍然几乎相同。  

即使用来优化网站的构建工具从未如此优秀和先进，但网站随附的JavaScript和CSS资源的平均（大小）权重仍在不断上升。  

随着[常青浏览器](https://www.techopedia.com/definition/31094/evergreen-browser)(可自动升级到未来版本的浏览器，可以理解为持续更新)市场份额的快速增长以及浏览器对web新功能的同步支持，是时候要重新考虑下现代网络中的资源交付了。

如今，大部分的web流量都是通过常青浏览器访问的，它们大多都支持ES6 +，新的JavaScript标准，新的Web平台API和CSS属性 。但是，在可观望的未来，旧版浏览器仍需要去支持，因为它们的使用份额还不少，远不能达到忽略的程度。当然，这个需求最终取决于您的用户群。

从[caniuse.com](https://caniuse.com/usage-table)中各版本浏览器的使用情况可以看出，常青浏览器的使用占据了浏览器市场的绝大部分，超过75％。尽管如此，但标准的做法还是添加CSS前缀，将所有的JavaScript转换到ES5标准，并包含polyfill，从而支持我们的每一个用户。

虽然从过往的发展历史来看这是可以理解的，因为web一直是渐进式增强的。但问题仍然存在：我们需要为了支持越来越少的旧版浏览器，而让大多数用户一起放慢web的步伐吗？

 ![The different compatibility layers of a web app.](/images/JS_Weekly/smart-bundling-legacy-code-browsers/01-shipping-a-modern-frontend.png)

<!-- more -->


## 支持旧版浏览器带来的成本

让我们试着从典型构建流程中了解，下面每个步骤是如何增加我们前端资源的负担的：

### 编译为ES5版本

为了评估这种转换带来的负担，我拿了一些用ES6+编写的流行JavaScript库，并比较了它们在转换前后的包大小：

| Library  | Size(minified ES6)  | Size  (minified ES5)  | Difference |
| ------------- | ------------- | ----- | ----|
|**TodoMVC**|8.4 KB|11 KB|24.5%|
|**Draggable**|77.9 KB|53.5 KB|31.3%|
|**Luxon**|75.4 KB|100.3 KB|24.8%|
|**Video.js**|237.2 KB|335.8 KB|29.4%|
|**PixiJS**|370.8 KB|452 KB|18%|

平均而言，未转换的包（ES6）比转换后的ES5的包小约25％。这并不奇怪，因为ES6+提供了更加紧凑和富有表现力的逻辑方式，而且将这些功能中的某些特性转换为ES5可能需要大量代码。

### ES6 + Polyfills
虽然Babel确实对ES6+语法有一个很好的支持，但ES6+引入的一些内置功能，比如`Promise`, `Map` 和 `Set`，和新的数组和字符串的方法，仍然需要polyfilled来做转换。  
而`babel-polyfill`本身，差不多就有90 KB。

### Web Platform Polyfills
现代Web提供了大量新的浏览器API来简化我们的应用程序开发。通常使用的，例如`fetch`，用于请求资源，`IntersectionObserver`用于有效地观察元素的可见性，以及`URL`规范，使得在web上读取和操作URL更容易。

为这些新功能添加符合规范的polyfill会对包大小产生明显影响。

### CSS Prefixing
最后，让我们看一下CSS前缀的影响。  
虽然前缀不会像其他构建转换那样增加包的大小，特别是因为它们在Gzip压缩时压缩得很好，但这里仍然可以节省一些成本。


| Library | Size (minified, prefixed for last 5 browser versions) | Size (minified, prefixed for last browser version)|Difference|
| ------------- | ------------- | ----- | ----|
|**Bootstrap**|159 KB|132 KB|17%|
|**Bulma**|184 KB|164 KB|10.9%|
|**Foundation**|139 KB|118 KB|15.1%|
|**Semantic UI**|622 KB|569 KB|8.5%|

## 一个发送有效代码的实用指南

做法是显而易见的。如果我们利用现有的构建管道，将这些兼容性层仅发送到需要它的浏览器，那就可以为其余用户（应该是大多数）提供更轻松的体验，同时保持了旧浏览器的兼容性。

 ![Forking our bundles.](/images/JS_Weekly/smart-bundling-legacy-code-browsers/modern-vs-legacy.png)


这个想法并不是全新的。  
[Polyfill.io](https://github.com/Financial-Times/polyfill-service)等服务就尝试过在运行时动态填充（polyfill）浏览器环境的做法。但是这样的方法有一些缺点：


*   除非您自己托管和维护polyfill服务，否则polyfills的选择仅限于服务已经列出的那些。
*   由于polyfilling在运行时提供服务并且是阻塞操作，因此旧浏览器上的用户页面的加载时间可能会明显增加。
*   向每个用户提供定制的polyfill文件会向系统引入熵，这会在出现问题时更难排查。

而且，这并没有解决代码转换带来的大小问题，有时反而会更大。

目前为止，我们已经发现了很多造成臃肿的来源，下面让我们看看，如何能够解决这些问题。

## 我们需要的工具

*   **Webpack**  
    这是我们选择的构建工具，你也可以选择其他构建工具（如Parcel和Rollup），它们使用起来很类似。
*   **Browserslist**  
    有了这个，我们将管理和定义我们想要支持的浏览器。 With this, we’ll manage and define the browsers we’d like to support.
*   然后，我们将使用一些 **支持Browserslist的插件** 。

### 1. 定义“流行”和“过期”
首先，我们要明确“流行”和“过期”浏览器的含义。这将有助于将浏览器分成两个独立的组而且便于维护和测试。我们把几乎不需要填充或转换的添加到“流行”列表的浏览器，并将其余部分放在我们的“过期”列表中。

 ![Browsers that support ES6+, new CSS attributes, and browser APIs like Promises and Fetch.](/images/JS_Weekly/smart-bundling-legacy-code-browsers/modern-browsers.png)


项目根目录下的Browserslist配置中可以存储此信息。“Environment”子部分可用于记录两个浏览器组，如下所示：

```
    [modern]
    Firefox >= 53
    Edge >= 15
    Chrome >= 58
    iOS >= 10.1

    [legacy]
    > 1%
```
此处给出的列表只是一个示例，您可以根据网站要求和时间进行自定义和更新。  
这个配置将作为我们接下来要创建的两组前端打包的判断依据：一个用于“流行”浏览器，另一个用于所有其他用户。

### 2. ES6+ Transpiling And Polyfilling
为了以环境感知的方式转换我们的JavaScript，我们将使用`babel-preset-env`。  
让我们在项目的根目录下初始化一个`.babelrc`文件：
```
    {
      "presets": [
        ["env", { "useBuiltIns": "entry"}]
      ]
    }
```
启用该`useBuiltIns`标志允许Babel选择性地polyfill ES6+引入的的部分内置功能。因为它可以选择环境所需的polyfill，所以我们可以降低`babel-polyfill`的整体运输成本。

要使此标志起作用，我们还需要在入口引入 `babel-polyfill`。
```
    // In
    import "babel-polyfill";
```
这样做会将babel-polyfill整体引入替换为按我们所定位的浏览器环境进行过滤的部分引入。
```
    // Transformed output
    import "core-js/modules/es7.string.pad-start";
    import "core-js/modules/es7.string.pad-end";
    import "core-js/modules/web.timers";
    …
```

### 3.Polyfilling Web Platform Features
要向我们的用户发送适用Web平台功能的polyfill，我们需要为这两个环境分别创建入口点：
```
    require('whatwg-fetch');
    require('es6-promise').polyfill();
    // … other polyfills
```
还有这个：
```
    // polyfills for modern browsers (if any)
    require('intersection-observer');
```
这是我们流程中唯一需要一定程度手动维护的步骤。通过将[eslint-plugin-compat](https://github.com/amilajack/eslint-plugin-compat) 添加到项目中，我们可以使这个过程不那么容易出错。当我们使用尚未polyfilled的浏览器功能时，此插件会警告我们。

### 4. CSS浏览器前缀
最后，让我们看看如何为不需要前缀的浏览器减少CSS前缀。
因为在`browserslist`生态系统中，`autoprefixer`是支持从配置文件读取内容的第一批工具，所以我们在这里没有太多工作要做。

在项目的根目录下创建一个简单的PostCSS配置文件应该足够了：
```
    module.exports = {
      plugins: [ require('autoprefixer') ],
    }
```

### 组合在一起
现在我们已经定义了所有必需的插件配置，我们可以合成一个webpack配置来读取上面的配置并输出两个单独的内置文件夹`dist/modern`和`dist/legacy`。

```js
    const MiniCssExtractPlugin = require('mini-css-extract-plugin')
    const isModern = process.env.BROWSERSLIST_ENV === 'modern'
    const buildRoot = path.resolve(__dirname, "dist")

    module.exports = {
      entry: [
        isModern ? './polyfills.modern.js' : './polyfills.legacy.js',
        "./main.js"
      ],
      output: {
        path: path.join(buildRoot, isModern ? 'modern' : 'legacy'),
        filename: 'bundle.[hash].js',
      },
      module: {
        rules: [
          { test: /\.jsx?$/, use: "babel-loader" },
          {
            test: /\.css$/,
            use: [MiniCssExtractPlugin.loader, 'css-loader', 'postcss-loader']
          }
        ]},
        plugins: {
          new MiniCssExtractPlugin(),
          new HtmlWebpackPlugin({
          template: 'index.hbs',
          filename: 'index.html',
        }),
      },
    };
```
然后，我们将在我们的`package.json`文件中创建一些构建命令：
```json
    "scripts": {
      "build": "yarn build:legacy && yarn build:modern",
      "build:legacy": "BROWSERSLIST_ENV=legacy webpack -p --config webpack.config.js",
      "build:modern": "BROWSERSLIST_ENV=modern webpack -p --config webpack.config.js"
    }
```
就是这些。现在，运行`yarn build`就会开始功能上等价的两个构建过程。

### 为用户提供合适的打包资源
创建单独的构建只是有助于实现我们目标的前半部分。下面，我们需要识别并向用户提供正确的打包资源。

还记得我们之前定义的浏览器列表配置吗？如果我们可以使用相同的配置来确定用户属于哪个类别，那不是很好吗？

了解下[browserslist-useragent](https://github.com/browserslist/browserslist-useragent)。顾名思义，`browserslist-useragent`可以读取我们的browserslist配置，然后将用户代理（User-Agent）与相关环境相匹配。  
使用Koa服务器演示以下示例：
```js
    const Koa = require('koa')
    const app = new Koa()
    const send = require('koa-send')
    const { matchesUA } = require('browserslist-useragent')
    var router = new Router()

    app.use(router.routes())

    router.get('/', async (ctx, next) => {
      const useragent = ctx.get('User-Agent')  
      const isModernUser = matchesUA(useragent, {
          env: 'modern',
          allowHigherVersions: true,
       })
       const index = isModernUser ? 'dist/modern/index.html':'dist/legacy/index.html'
       await send(ctx, index);
    });
```
在这里，设置`allowHigherVersions`标志可以确保如果浏览器发布较新版本，即使是那些尚未进入Can I Use数据库的版本，它们仍然会报告为“流行”浏览器。

`browserslist-useragent`的其中一个功能是确保在匹配用户代理时考虑平台怪癖。例如，iOS上的所有浏览器（包括Chrome）都使用WebKit作为底层引擎，但在Browserslist查询中，会与相应的特定Safari浏览器进行匹配。

单纯依赖生产中user-agent解析的正确性可能并不谨慎。对未在“流行”列表中定义的浏览器和具有未知或不可解析的用户代理字符串的情况，我们通过回退到“过期”浏览器打包文件，来确保我们的网站仍然有效。

### 结论：值得吗？
我们已经为客户传输最小的打包文件，设法覆盖到端到端的流量。看起来已经很棒了，但是，权衡项目的维护成本和利益是合理的。
让我们评估一下这种方法的优缺点：

#### 1.维护和测试
只需要维护一个Browserslist配置即可为该构建中的所有工具提供支持。
“流行”和“过期”浏览器的定义可以在将来的任何时候更新，且无需重构配置或代码。  
我认为这使维护的开销几乎可以忽略不计。

不过，通过Babel生成两个不同的，且都需要在各自的环境中正常工作的代码包有很小的理论风险。

虽然由于打包差异导致的错误可能很少，但监控这些错误有助于有效识别并缓解问题。


#### 2. 花费构建时间 vs 花费运行时间
与当今流行的其他技术(比如，上文提到的Polyfill.io)不同，所有这些优化都在构建时发生，并且对客户端是不可见的。

#### 3. 逐步提高速度
在这种方式下，使用“流行”浏览器的用户会比以前感觉速度更快，而旧版浏览器上的用户继续像以前一样，也没有任何负面效果。

#### 4. 轻松使用现代浏览器功能
在这种方式之前，我们经常会由于担心所需的polyfill的太大，而不敢使用新的浏览器功能，甚至有时候会选择较小的不符合规格的polyfill来节省尺寸。  
这种新方法允许我们使用符合规范的polyfill，而不必担心影响所有用户。

### 生产服务中的差异化打包
鉴于其显着的优势，我们在为印度最大的家具和装饰零售商之一Urban Ladder的客户创建新的移动结账体验时采用了这种构建方式。

在我们已经优化的打包中，对于移动用户，我们能够将Gzip压缩过的CSS和JavaScript资源再节省大约20％。
因为超过80％的用户都使用这些常青浏览器上，所以付出的努力非常值得。

## 资源
* 原文: https://www.smashingmagazine.com/2018/10/smart-bundling-legacy-code-browsers/
* 当前浏览器市场份额表： [Can I Use](https://caniuse.com/usage-table)  
* [Browserslist “Tools”](https://github.com/browserslist/browserslist#tools)
* [`@babel/preset-env`](https://babeljs.io/docs/en/babel-preset-env)  
* [Loading Polyfills Only When Needed](https://philipwalton.com/articles/loading-polyfills-only-when-needed/)
