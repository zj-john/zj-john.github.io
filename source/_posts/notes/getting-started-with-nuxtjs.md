---
title: Nuxt.js入门
categories:
  - notes
  - vue
  - javascript
  - 2018.10.12
tags:
  - JavaScript Weekly
toc: true
date: 2018-10-17 12:51:15
---

![Vue](/images/JS_Weekly/getting-started-with-nuxtjs/vuel_870x2203f45cdf5cd6c480aa55f7525dfd1eba3.png)

[Nuxt.js](https://zh.nuxtjs.org/)是一个基于 Vue.js 的通用应用框架，其主要目的是构建一个可扩展的，高品质的Vue.js应用程序。Nuxt有哪些优点，是如何实现的，该如何安装它，以及如何在下一个项目中使用它呢？

本文为您答疑解惑。

<!-- more -->

Nuxt.js是基于[Vue.js](https://vuejs.org/)，[Vue Router](https://router.vuejs.org/)，[Vuex](https://vuex.vuejs.org/), [Vue Server Renderer](https://ssr.vuejs.org/)和[v](https://github.com/declandewet/vue-meta)[ue](https://github.com/declandewet/vue-meta)-[m](https://github.com/declandewet/vue-meta)[eta](https://github.com/declandewet/vue-meta)插件的一个通用框架，它提供了丰富的工具集，来满足构建任何类型的Web应用程序。

它之所以被称为通用框架，是因为它的构建足够灵活，可以满足从静态站点到单页应用程序的任何项目。

它主要关注开发的UI渲染方面，同时试图抽象出客户端/服务器分布。

## 是什么让Nuxt.js如此特别？

1. **基于Vue**： Nuxt.js是基于Vue.js构建的，所以继承了vue本身的所有优势。事实上，Nuxt.js旨在使您能够编写最佳的Vue.js代码。
2. **自动路由处理** ：Nuxt.js基于Vue-router处理路由，改进点是会根据pages文件夹中的Vue文件结构自动生成所需的configuration/routes配置/路由。这意味着你不必费心去设置Vue-router配置，因为Nuxt会为你做这件事。
3. **服务器端渲染**：Nuxt.js使用Vue Server Renderer插件来处理服务器端的渲染，但是像之前提过的那样，它封装了所有艰涩的操作并提供自动处理，同时还可轻松修改meta标记的属性。
4. **静态站点**：Nuxt.js有一个nuxt generate命令，可以为路由中的每个页面生成HTML静态版本，并将它们存储在一个文件中，您可以在任何静态托管平台上托管该文件。 
5. **基于Webpack**：Nuxt.js使用带有vue-loader和babel-loader的webpack来打包，压缩，转换ES6/ES7并对代码进行代码分割，能满足你遇到的所有情况。
6. **HTTP/2**：Nuxt.js为我们提供了一个属性，可以在我们的应用程序中激活HTTP/2推送头。HTTP/2 Push是一种功能，它允许服务器在没有相应请求的情况下将资源推送到客户端（即没有对该资源的立即请求）。
7. **开发中的热模块替换**：由于webpack和vue-loader，Nuxt.js在应用运行时，对对代码所做的更改实时更新视图，而无需重新加载整页。

## 安装Nuxt.js
Nuxt.js团队在Vue CLI 3上创建了一个初始化模板，通过这个模板，可以快速使用Vue CLI来构建启动Nuxt.js项目，而无需担心分散复杂的开发环境和依赖。

因为该模板是适配Vue CLI构建的，所以如果您尚未安装Vue Cli，则请先安装。

要安装Vue CLI，请启动终端并运行以下命令：
```shell
  npm install -g @vue/cli @vue/cli-init
```
接下来，安装初始化模板并使用它来生成新项目：

>project-name不能包含大写字母哦

```
vue init nuxt-community/starter-template <project-name>
```
当安装完成并创建项目后，将目录切换到创建的新项目文件夹并安装所有依赖项。
```
cd <project-name> // Change Directory
npm install // Install dependencies
```
最后，当所有以上步骤完成后，我们使用npm run dev启动项目。应用程序应该成功运行在[http://localhost:3000](http://localhost:3000/)。

## 文件目录结构
浏览下新项目，我们注意到已经创建了一些文件夹/文件; 它们旨在为我们的应用程序提供一个很好的模板结构。

让我们快速浏览每个文件夹的内容。

**Assets**: 存放我们所有的外部资源文件（例如图像，CSS和我们的应用程序可能需要的任何其他文件）。

**Components:**： 包含可在我们的应用程序中使用的可重用组件（例如按钮，输入，侧边栏等）。

**Layouts:** 包含我们的布局文件，这些布局将在我们的应用程序中全面重用。 **注意:** 此文件夹不能也不应该重命名。  

**Middleware**: 顾名思义，它包含了我们应用程序的中间件。中间件是在呈现组件之前需要运行的函数（例如，在显示页面之前检查用户是否已登录）。

**Pages:** 此文件夹包含我们的应用程序的视图和路由，因为Nuxt.js会读取此文件夹中的所有文件，并根据文件自动创建我们的应用程序路由。**注意:** 此文件夹也无法重命名。

**Plugins:** 包含我们在程序根Vue.js运行之前需要运行的所有JavaScript插件（前置依赖）。

**Static:** 它与Assets文件夹非常相似，区别是Assets文件夹用于需要由webpack解析的文件（即需要某种编译\[SASS→CSS\]或处理的文件），而Static文件夹用于已经处理或处于最终状态的资源和文件。

## 添加Assets
-------------
感谢webpack，vue-loader，file-loader和url-loader，Nuxt.js简化了我们在项目文件夹中使用资源的方式。

要使用assets文件夹中的文件，您需要在Assets文件夹名称之前添加〜/，如下所示：
```html
<img src="~/assets/image.png">
```
但是，如果要使用Static文件夹中的文件，您只需使用/，就好像这些文件存在于根目录中一样。
```html
<img src="/image.png">
```
你可能想知道为什么。

好吧，Assets文件夹中的文件需要特殊字符〜，主要是因为默认情况下webpack中的Vue-loader插件会将文件解析为模块依赖项，而〜只是一个别名，告诉Vue-loader Assets文件夹所在的位置。

使用Vue-loader解析资源的一个很酷的部分是，它还有助于处理资源的版本控制以获得更好的缓存处理，同时，如果某个资源小于1KB，它们将处理为内联base-64的数据以减少这些较小文件的HTTP请求数。

另一方面，Static文件夹由Nuxt自动提供，并在构建生产时移动到项目的根目录中。
```html
<!-- Image in the static folder -->
<img src="/image.png"/>

<!-- Image in the assets Folder -->
<img src="~/assets/image-2.png"/>
```

## 组件
组件是通过名称可重用Vue实例。当我们在Nuxt中创建页面时，我们也创建了一个组件，并且每个组件都有一系列使其工作的函数。

Nuxt.js引入了一些额外的功能，属性或方法，以提供更多功能来帮助组件的开发。您可以视情况使用。

这些额外属性是什么？

**asyncData:** asyncData钩子允许您在渲染页面之前，从服务器上获取页面所需的数据。

这样，当Google尝试索引您的网页时，网页会始终包含内容 - asyncData钩子会为其提供数据。

**fetch**: 此方法处理与store的交互。它允许您即使在渲染组件之前，也可以自由地向Vuex store提供数据。It allows you the liberty of providing data to the Vuex store even before the component is rendered on the view.

这对于在呈现所述组件之前另一个组件需要来自另一个组件的数据时是完美的。
This is perfect for when another component needs data from say another component before said component is rendered. 

**loading**: Nuxt.js在应用中提供加载状态，此方法允许您中断加载状态并手动控制它。

**layout**: 设置应在中使用layouts目录中的哪个布局。

**transition**: 允许您为页面组件设置特定转换。

**scrollToTop**: 此方法允许您指定是否希望在渲染页面之前自动滚动到页面顶部。

**validate**: validate钩子允许您创建一个验证器方法，该方法检查动态路由中传递的参数类型。

当你想要确保它传递给url参数的是数字而不是字符串时，这很有用，反之亦然。

**middleware:** 允许您指定该组件的中间件。

## 路由
Nuxt.js中的路由非常有趣。它会查看Pages文件夹中的文件结构，并根据该结构自动决定路由配置。

您所要做的就是按照您希望的URL的结构构建Pages文件夹。

它利用Vue Router插件生成路由; 因此，生成的配置与您已知的在Vue.js应用程序中使用Vue Router配置相同。

虽然有一点点扭曲：你可以使用<nuxt-link>而不是像通常那样使用<router-link>。

Nuxt.js还会处理404页面请求错误，例如当页面或路由不可用但用户导航到它时。Nuxt有一个默认的404页面，但也提供了自定义或使用自己的404页面的方法。

现在，让我们深入研究一些例子，因为我相信你已经想知道如何处理动态路由等等问题了。

### 基本路由
基本的文件目录结构是一个文件夹下包含需要的视图文件，如下所示：
```
pages/

--| work/

-----| index.vue

-----| progress.vue

-----| telerik.vue

--| index.vue
```
Nuxt会基于上面的目录结构生成以下路由：
```js
router: {
  routes: [
    {
      name: 'index',
      path: '/',
      component: 'pages/index.vue'
    },
    {
      name: 'work',
      path: '/work',
      component: 'pages/user/index.vue'
    },
    {
      name: 'work-progress',
      path: '/work/progress',
      component: 'pages/work/progress.vue'
    },
    {
      name: 'work-telerik',
      path: '/work/telerik',
      component: 'pages/work/telerik.vue'
    }
  ]
}
```

### 动态路由
动态路由是在其URL中采用动态参数的路由。为此，我们必须使用下划线为.vue文件或文件夹的名称添加前缀。

```
pages/

--| _slug/

-----| comments.vue

-----| index.vue

--| users/

-----| _id.vue

--| index.vue
```
路由如下：
```
router: {
  routes: [
    {
      name: 'index',
      path: '/',
      component: 'pages/index.vue'
    },
    {

      name: 'users-id',
      path: '/users/:id?',
      component: 'pages/users/_id.vue'
    },
    {
      name: 'slug',
      path: '/:slug',
      component: 'pages/_slug/index.vue'
    },
    {
      name: 'slug-comments',
      path: '/:slug/comments',
      component: 'pages/_slug/comments.vue'
    }
  ]
}
```
  

### 嵌套路由
```
pages/

--| work/

-----| _id.vue

-----| index.vue

--| work.vue
```
路由如下：

```
router: {
  routes: [
    {
      path: '/work',
      component: 'pages/work.vue',
      children: [
        {
          path: '',
          component: 'pages/work/index.vue',
          name: 'work'
        },
        {
          path: ':id',
          component: 'pages/work/_id.vue',
          name: 'work-id'
        }
      ]
    }
  ]
}
```

## 设置页面元标记
Nuxt.js为我们提供了能够更新页面的标题，meta和HTML属性的属性。它在后台使用vue-meta来完成所有这些伟大的工作。

要在页面组件中设置头元素（如meta和链接），您必须使用nuxt.js提供的页面组件中的head属性。

```html
<template>
    <section class="container">
        <div>
            <h1 class="title">Nuxt.js Page</h1>
        </div>
    </section>
</template>
<script>
export default {
    head: {
        meta: [
            { charset: 'utf-8' },
            { name: 'viewport', content: 'width=device-width, initial-scale=1' }
        ],
        link: [
            { rel: 'stylesheet', href: 'https://fonts.googleapis.com/css?family=Open+Sans' }
        ]
    },
    data() {
        return {
        }
    }
}
</script>
```

## 自定义错误页面
默认情况下，Nuxt.js配置了自己的错误页面，但它也允许您指定自己的自定义错误页面。

您需要做的就是进入layouts文件夹并创建一个名为error.vue的vue组件，并且：当任何时候出现404或500错误时，Nuxt会向客户端显示这个新的错误页面。

## 部署Nuxt.js应用程序
根据您要构建的内容，有三种不同的模式可用于准备我们的生产发布。

### 服务器端渲染 
要构建应用程序，您只需运行以下代码即可：
```
npm run build
```
这个命令深入我们的代码，分析，生成路由，编译需要编译的文件，然后创建一个名为.nuxt的文件夹，并把所有这些生产需要的文件移动到这里。

现在我们有了一个完全服务器端渲染的Vue.js应用程序。
> 生成标准的vue程序

### 静态站点生成tatic Site Generated
要将我们的应用程序生成为静态文件，我们必须运行以下命令：

```
npm run generate
```
此命令编译我们的代码，生成路由并内联生成的每个静态页面中的所有URL，并将它们存储在dist文件夹中。

### 单页应用程序
使用nuxt.js生成单个页面应用程序可以通过两种方式完成。

1. 添加模式'spa'到nuxt.config.js。
2. 在package.json中将--spa标志添加到脚本中，如下所示：

```json
"scripts": {
    "dev": "nuxt --spa",
    "build": "nuxt build --spa",
    "start": "nuxt start",
    "generate": "nuxt generate",
    "lint": "eslint --ext .js,.vue --ignore-path .gitignore .",
    "precommit": "npm run lint"
  },
```

## 资源
* 原文 https://www.telerik.com/blogs/getting-started-with-nuxtjs
* nuxt https://zh.nuxtjs.org/
