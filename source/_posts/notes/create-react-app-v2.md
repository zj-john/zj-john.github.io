---
title: Create React App 2.0发布- Babel 7, Sass, and More
categories:
  - notes
  - create react app
  - react-scripts
  - 2018.10.05
tags:
  - JavaScript Weekly
toc: true
date: 2018-10-09 14:12:45
---

Create React App 2.0已于2018.10.01发布，在本次版本升级中极大地更新完善了产品功能和体验。

*Create React App 2.0 has been released today, and it brings a year’s worth of improvements in a single dependency update.*

虽然使用React本身不需要任何构建相关的依赖性，但编写一个含有快速测试模块，生产用压缩工具和模块化代码库的复杂应用程序仍然具有挑战性。从第一个版本开始，[Create React App](https://github.com/facebook/create-react-app)的目标就是帮助您专注于最重要的事项 - 您的应用程序代码 - 并为您处理构建和测试的相关设置。

*While React itself [doesn’t require any build dependencies](/docs/create-a-new-react-app.html), it can be challenging to write a complex app without a fast test runner, a production minifier, and a modular codebase. Since the very first release, the goal of [Create React App](https://github.com/facebook/create-react-app) has been to help you focus on what matters the most — your application code — and to handle build and testing setup for you.*

它依赖的许多工具到现在为止已经发布了包含新功能和性能改进的新版本：[Babel 7]((https://babeljs.io/blog/2018/08/27/7.0.0))，[webpack 4](https://medium.com/webpack/webpack-4-released-today-6cdb994702d4)和[Jest 23](https://jestjs.io/blog/2018/05/29/jest-23-blazing-fast-delightful-testing.html)。但是，手动更新它们并使它们一起协同工作需要花费很多精力。这正是Create React App 2.0贡献者在过去几个月一直忙于的事情：**迁移配置和依赖项**，这样您就不需要自己动手了。

*Many of the tools it relies on have since released new versions containing new features and performance improvements: [Babel 7](https://babeljs.io/blog/2018/08/27/7.0.0), [webpack 4](https://medium.com/webpack/webpack-4-released-today-6cdb994702d4), and [Jest 23](https://jestjs.io/blog/2018/05/29/jest-23-blazing-fast-delightful-testing.html). However, updating them manually and making them work well together takes a lot of effort. And this is exactly what [Create React App 2.0 contributors](https://github.com/facebook/create-react-app/graphs/contributors) have been busy with for the past few months: **migrating the configuration and dependencies so that you don’t need to do it yourself.***

现在Create React App 2.0已经脱离了测试版，让我们看看有什么新东西以及如何尝试它！
*Now that Create React App 2.0 is out of beta, let’s see what’s new and how you can try it!*

> 注意
>
>不要为升级感到有压力。如果您对当前在用的版本的性能和可靠性感到满意，则可以继续使用当前版本！在生产中切换到2.0之前，让2.0版本稳定一点可能也是一个好主意。
>
> *Don’t feel pressured to upgrade anything. If you’re satisfied with the current feature set, its performance, and reliability, you can keep using the version you’re currently at! It might also be a good idea to let the 2.0 release stabilize a little bit before switching to it in production.*


<!-- more -->

## 新功能

以下是此版本中新功能的简短摘要：

* 🎉更多样式选项：您可以直接使用[Sass](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#adding-a-sass-stylesheet)和[CSS模块](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#adding-a-css-modules-stylesheet)。
* 🐠我们更新到[Babel 7](https://babeljs.io/blog/2018/08/27/7.0.0)，包括对[React fragment syntax](/docs/fragments.html#short-syntax) 和许多bugfixes的支持。
* 📦我们更新到[webpack 4](https://medium.com/webpack/webpack-4-released-today-6cdb994702d4)，它可以更智能地自动拆分JS包。
* 🃏我们更新到[Jest 23](https://jestjs.io/blog/2018/05/29/jest-23-blazing-fast-delightful-testing.html)，其中包括用于[查看快照的交互模式]((https://jestjs.io/blog/2018/05/29/jest-23-blazing-fast-delightful-testing#interactive-snapshot-mode))。
* 💄我们添加了[PostCSS](https://preset-env.cssdb.org/features#stage-3)，因此您可以在旧浏览器中使用新的CSS功能。
* 💎您可以使用[Apollo](https://github.com/leoasis/graphql-tag.macro#usage)，[Relay Modern](https://github.com/facebook/relay/pull/2171#issuecomment-411459604)，[MDX](https://github.com/facebook/create-react-app/issues/5149#issuecomment-425396995)和其他第三方[Babel Macros](https://babeljs.io/blog/2017/09/11/zero-config-with-babel-macros)变换。
* 🌠现在可以[将SVG作为React组件导入]((https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#adding-svgs))，并在JSX中使用它。
* 🐈您可以尝试实验性的[Yarn Plug’n’Play mode](https://github.com/yarnpkg/rfcs/pull/101)，将删除的`node_modules`。
* 🕸现在，您可以在开发中[插入自己的代理实现]((https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#configuring-the-proxy-manually))，以匹配您的后端API。
* 🚀您现在可以使用[最新Node版本编写的包](https://github.com/sindresorhus/ama/issues/446#issuecomment-281014491)而不会破坏构建。
* ✂️如果您只打算适配现代浏览器，现在可以选择获得较小的CSS包。
* 👷♀️Service workers现在可以选择性加入，它使用Google的[Workbox](https://developers.google.com/web/tools/workbox/)构建。


*   🎉 More styling options: you can use [Sass](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#adding-a-sass-stylesheet) and [CSS Modules](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#adding-a-css-modules-stylesheet) out of the box.
*   🐠 We updated to [Babel 7](https://babeljs.io/blog/2018/08/27/7.0.0), including support for the [React fragment syntax](/docs/fragments.html#short-syntax) and many bugfixes.
*   📦 We updated to [webpack 4](https://medium.com/webpack/webpack-4-released-today-6cdb994702d4), which automatically splits JS bundles more intelligently.
*   🃏 We updated to [Jest 23](https://jestjs.io/blog/2018/05/29/jest-23-blazing-fast-delightful-testing.html), which includes an [interactive mode](https://jestjs.io/blog/2018/05/29/jest-23-blazing-fast-delightful-testing#interactive-snapshot-mode) for reviewing snapshots.
*   💄 We added [PostCSS](https://preset-env.cssdb.org/features#stage-3) so you can use new CSS features in old browsers.
*   💎 You can use [Apollo](https://github.com/leoasis/graphql-tag.macro#usage), [Relay Modern](https://github.com/facebook/relay/pull/2171#issuecomment-411459604), [MDX](https://github.com/facebook/create-react-app/issues/5149#issuecomment-425396995), and other third-party [Babel Macros](https://babeljs.io/blog/2017/09/11/zero-config-with-babel-macros) transforms.
*   🌠 You can now [import an SVG as a React component](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#adding-svgs), and use it in JSX.
*   🐈 You can try the experimental [Yarn Plug’n’Play mode](https://github.com/yarnpkg/rfcs/pull/101) that removes `node_modules`.
*   🕸 You can now [plug your own proxy implementation](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#configuring-the-proxy-manually) in development to match your backend API.
*   🚀 You can now use [packages written for latest Node versions](https://github.com/sindresorhus/ama/issues/446#issuecomment-281014491) without breaking the build.
*   ✂️ You can now optionally get a smaller CSS bundle if you only plan to target modern browsers.
*   👷‍♀️ Service workers are now opt-in and are built using Google’s [Workbox](https://developers.google.com/web/tools/workbox/).

**所有这些功能都是开箱即用的** - 要启用它们，请按照以下说明操作。

**All of these features work out of the box** — to enable them, follow the below instructions.

## 使用Create React App 2.0开启项目

您无需更新任何特殊内容。从今天开始，当您运行`create-react-app`时，它默认情况下将使用2.0版本的模板。用的开心！

You don’t need to update anything special. Starting from today, when you run `create-react-app` it will use the 2.0 version of the template by default. Have fun!

如果由于某种原因想要使用旧的1.x模板，可以通过将`--scripts-version=react-scripts@1.x`作为参数来实现`create-react-app`。

If you want to **use the old 1.x template** for some reason, you can do that by passing `--scripts-version=react-scripts@1.x` as an argument to `create-react-app`.

## 更新项目以创建React App 2.0
将non-ejected项目升级到Create React App 2.0通常应该很简单。打开项目的根目录中的`package.json`，找到 `react-scripts`，

*Upgrading a non-ejected project to Create React App 2.0 should usually be straightforward. Open `package.json` in the root of your project and find `react-scripts` there.*

然后将其版本更改为`2.0.3`：
*Then change its version to `2.0.3`:*
```json
      // ... other dependencies ...
      "react-scripts": "2.0.3"
```

运行`npm install`（或者`yarn`，如果您使用它）。**对于许多项目，这种一行代码的更改足以升级！**

Run `npm install` (or `yarn`, if you use it). **For many projects, this one-line change is sufficient to upgrade!**


这里有一些技巧可以帮助您入门。

Here’s a few more tips to get you started.

**当您在升级后第一次运行`npm start`时**，您会收到一个提示，询问您希望支持哪些浏览器。按`y`接受默认值。它们会写入您的`package.json`，您可以随时编辑它们。Create React App将使用此信息生成较小或polyfilled的CSS包，具体取决于您是以现代浏览器还是旧浏览器为目标。

**When you run `npm start` for the first time after the upgrade,** you’ll get a prompt asking about which browsers you’d like to support. Press `y` to accept the default ones. They’ll be written to your `package.json` and you can edit them any time. Create React App will use this information to produce smaller or polyfilled CSS bundles depending on whether you target modern browsers or older browsers.

**如果升级后`npm start`仍然无法正常工作**， 请查看发行说明中更详细的[迁移说明](https://github.com/facebook/create-react-app/releases/tag/v2.0.3)。此版本中是有几个重大的变动，但他们的范围是有限的，所以不应该花费超过几个小时进行梳理。请注意，现在可以[选择支持旧版浏览器](https://github.com/facebook/create-react-app/blob/next/packages/react-app-polyfill/README.md) 是可选的，通过它可以减少polyfill大小。

**If `npm start` still doesn’t quite work for you after the upgrade,** [check out the more detailed migration instructions in the release notes](https://github.com/facebook/create-react-app/releases/tag/v2.0.3). There _are_ a few breaking changes in this release but the scope of them is limited, so they shouldn’t take more than a few hours to sort out. Note that **[support for older browsers](https://github.com/facebook/create-react-app/blob/next/packages/react-app-polyfill/README.md) is now opt-in** to reduce the polyfill size.

**如果您之前ejected但现在想要升级**，一个常见的解决方案是找到您ejected的提交（以及任何后续提交更改配置），还原它们，升级，然后再选择再次eject。您现在支持开箱即用的功能（可能是Sass或CSS模块？）也是可能的。

**If you previously ejected but now want to upgrade,** one common solution is to find the commits where you ejected (and any subsequent commits changing the configuration), revert them, upgrade, and later optionally eject again. It’s also possible that the feature you ejected for (maybe Sass or CSS Modules?) is now supported out of the box.

> Note
>
>由于npm中可能存在的bug，您可能会看到有关未满足的对等依赖项的警告。你应该可以忽略它们。据我们所知，Yarn没有这个问题。
>
> Due to a possible bug in npm, you might see warnings about unsatisfied peer dependencies. You should be able to ignore them. As far as we’re aware, this issue isn’t present with Yarn.


## 突破性变化
以下是此版本中重大更改的简短列表：
Here’s a short list of breaking changes in this release:

* 不再支持Node 6。
* 对旧版浏览器（例如IE 9到IE 11）的支持现在通过使用[单独的软件包](https://github.com/facebook/create-react-app/tree/master/packages/react-app-polyfill)来选择。
* 现在通过`import()`来进行代码分割的行为更接近于规范，同时`require.ensure()`被禁用。
* 默认的Jest环境现在包含jsdom。
* 支持将指定对象设置为`proxy`设置已替换为对自定义代理模块的支持。
* 对`.mjs`扩展的支持被删除，直到它的生态系统稳定下来。
* PropTypes定义会自动从生产版本中删除。

*   Node 6 is no longer supported.
*   Support for older browsers (such as IE 9 to IE 11) is now opt-in with a [separate package](https://github.com/facebook/create-react-app/tree/master/packages/react-app-polyfill).
*   Code-splitting with `import()` now behaves closer to specification, while `require.ensure()` is disabled.
*   The default Jest environment now includes jsdom.
*   Support for specifying an object as `proxy` setting was replaced with support for a custom proxy module.
*   Support for `.mjs` extension was removed until the ecosystem around it stabilizes.
*   PropTypes definitions are automatically stripped out of the production builds.

如果这些点中的任何一个对您有影响，则[2.0.3发行说明](https://github.com/facebook/create-react-app/releases/tag/v2.0.3)包含更详细的说明。
If either of these points affects you, [2.0.3 release notes](https://github.com/facebook/create-react-app/releases/tag/v2.0.3) contain more detailed instructions.

## 更多
您可以在[发行说明](https://github.com/facebook/create-react-app/releases/tag/v2.0.3)中找到完整的更改日志。这是一个很大的更新，我们可能错过了一些东西。请向我们的提[issue](https://github.com/facebook/create-react-app/issues/new)报告任何问题，我们会尽力提供帮助。

You can find the full changelog in the [release notes](https://github.com/facebook/create-react-app/releases/tag/v2.0.3). This was a large release, and we may have missed something. Please report any problems to our [issue tracker](https://github.com/facebook/create-react-app/issues/new) and we’ll try to help.

> Note
>
>如果您一直在使用2.x alpha版本，我们会为它们提供单独的[迁移说明](https://gist.github.com/gaearon/8650d1c70e436e5eff01f396dffc4114)
>
> If you’ve been using 2.x alpha versions, we provide [separate migration instructions](https://gist.github.com/gaearon/8650d1c70e436e5eff01f396dffc4114) for them.


## 链接
* https://reactjs.org/blog/2018/10/01/create-react-app-v2.html
