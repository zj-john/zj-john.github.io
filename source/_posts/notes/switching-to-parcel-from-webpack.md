---
title: 从Webpack到Parcel
categories:
  - notes
tags:
  - JavaScript Weekly
  - webpack
  - parcel
  - 2018.11.30
toc: true
date: 2018-12-04 10:04:53
---


![Switching to Parcel from Webpack](/images/JS_Weekly/switching-to-parcel-from-webpack/31321658-f6aed0f2-ac3d-11e7-8100-1587e676e0ec.png)

过去的一年中，您可能听过Parcel，一个新的构建工具。
对于那些还没有接触过它的人，Parcel将自己描述为 _“一个Web应用程序打包器，利用多核处理提供极快的性能，并且需要零配置”_。
现在构建工具很丰富，特别是有像Webpack和Browserify这样的优秀工具。
所以为什么我们需要一个新工具Parcel？

<!-- more -->
### 原因
因为Webpack不适用于初学者。  
即使只做一些基础功能，比如对Sass/SCSS、ES6、浏览器前缀的处理，代码压缩、hash命名，dev-server等，也需要在webpack中进行大量配置，而且每种配置的可选值也多到连作者都说不清（这个梗是在GitHub上看到webpack作者回复的一个issue）。

> 说实话，虽然用了很久的webpack，但每次开新项目或修改配置，我还是会回到文档查询。因为要记的东西太多了，离开文档无法存活……

为了解决这些问题，于是有了parcel。

### parcel
极速零配置Web应用打包工具。
* **极速打包**：Parcel 使用 worker 进程去启用多核编译。同时有文件系统缓存，即使在重启构建后也能快速再编译。
* **将你所有的资源打包**：Parcel 具备开箱即用的对 JS, CSS, HTML, 文件 及更多的支持，而且不需要插件。
* **零配置代码分拆**：使用动态 import() 语法, Parcel 将你的输出文件束(bundles)分拆，因此你只需要在初次加载时加载你所需要的代码。
* **自动转换**：如若有需要，Babel, PostCSS, 和PostHTML甚至 node_modules 包会被用于自动转换代码.
* **热模块替换**：Parcel 无需配置，在开发环境的时候会自动在浏览器内随着你的代码更改而去更新模块。
* **友好的错误日志**：当遇到错误时，Parcel 会输出 语法高亮的代码片段，帮助你定位问题。

> 从这些特性看，文件缓存系统这个功能还是挺好的，对比webpack的每次启动实在是太慢了。后面的这些都像是webpack的语法糖。对于不太在乎打包性能的，比如内部工具来说，是一个比较便捷的方式。但对于性能要求高的外部应用，还是用webpack去做优化配置更好一些。

### 转换前
下面，我们将采用一个典型的Webpack设置，其中包含前面提到的所有插件，然后我们将其换成Parcel。

让我们看一下 **webpack.config.js** 文件，评估下将要转换的内容：
```js
module.exports = {
  entry: { main: './src/js/main.js' },
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].[chunkhash].js'
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        }
      },
      {
        test: /\.s[c|a]ss$/,
        use: ['style-loader', MiniCssExtractPlugin.loader, 'css-loader', 'postcss-loader', 'sass-loader']
      }
    ]
  },
  plugins: [
    new CleanWebpackPlugin('dist', {}),
    new MiniCssExtractPlugin({
      filename: 'style.[contenthash].css',
    }),
    new HtmlWebpackPlugin({
      inject: false,
      hash: true,
      template: './src/html/index.html',
      filename: 'index.html'
    }),
    new WebpackMd5Hash()
  ]
};
```
除了安装webpack和webpack-cli之外，我们还必须安装以下依赖项：autoprefixer，babel-core，babel-loader，babel-preset-env，clean-webpack-plugin，css-loader，extract-text-webpack-plugin，html-webpack-plugin，mini-css-extract-plugin，node-sass，postcss-loader，sass-loader，style-loader和webpack-md5-hash。我还需要设置一个 **postcss.config.js** 文件，以便浏览器前缀可以工作。

看吧，为了实现一些基础功能，我们已经需要进行大量配置了。

### 转换
为了与我们的Webpack设置保持一致，我们希望Parcel设置能够处理以下事情：

*   Sass/SCSS;
*   Babel;
*   Vendor Prefixing;
*   Code Minification;
*   Cache-Busting/Hashing;
*   Live Server whipping-uppering.

要怎么做？
首先，我们安装Parcel。
```
npm install -g parcel-bundler
```
这里进行全局安装，以便我们运行parcel命令时无需包含整个文件路径。

如果您愿意，可以选择省略-g并在项目内部进行本地安装。此时，需要在终端Node中运行 node_modules/parcel-bundler/bin/ cli.js.

然后我去创建了一个初始项目结构，如下所示：
```
/dist
/src/js/script.js
/src/scss/styles.scss
Index.html
```
我进入项目根目录并在终端中运行
```
//无命令界面，以默认方式创建
npm init -y
```
创建 **package.json** 文件
我把一些初始代码放在 **script.js** 和 **styles.scss** 中作为测试代码。没有什么太花哨的：我在 **script.js** 中创建了一个const，看它是否会被编译成var，并编写了一些非常基本的嵌套SCSS代码，看它是否会被编译成CSS。

js:
```js
const msg = "HelloWorld"
alert(msg);
```
css:
```css
body {
  h1 {
    color: red
  }
}
```

Parcel会从index.html中的脚本开始，编译它找到的代码。

所以在我们的情况下，我在index.html中有以下内容

```html
<html>
  <body>
    <h1>Hello, Parcel!</h1>
    <script src="src/js/script.js"></script>
  </body>
</html>
```

然后我从终端运行了parcel index.html。几秒钟后，Parcel将一些文件编译到/dist目录并在端口1234启动了本地服务器。  

> 请注意，此时我们并没有运行npm install,目录下也没有node_module目录，但是已经可以运行了

打开浏览器并转到localhost:1234，可以看到一切正常！我检查了编译后的脚本文件（它还有一个哈希后缀来处理缓存清除），并且可以看到const确实已经变成了var。

因此，在我没有配置任何东西的情况下，Parcel已经生成一个本地服务器，使用Babel将ES6代码转换为ES5，甚至处理文件hash。

> 我们可以通过简单地在命令的末尾添加-open来让Parcel打开你的默认浏览器并加载页面。

### css部分
下面，我们在script.js文件的顶部添加了import '../scss/styles.scss'。
```js
import '../scss/styles.scss'
const msg = "HelloWorld"
alert(msg);
```
为了使scss生效，我们首先安装node-sass。
在终端上运行
```
npm install node-sass
```

然后运行parcel index.html，此时效果已经有了。

此时，dist文件夹中包含用于HTML，CSS和JS单独的文件 ，Parcel使用了895ms来编译所有内容，这非常快速！

### 浏览器前缀
我在我的SCSS中添加了一个::placeholder标记，看看会发生什么。
```html
<html>
  <body>
    <h1>Hello, Parcel!</h1>
    <input placeholder="我是绿色的"/>
    <script src="src/js/script.js"></script>
  </body>
</html>
```
css
```
body {
  h1 {
    color: red
  }  
}
input::placeholder {
  color: green;
}
```
此时编译的CSS代码不包含任何前缀。
我们所要做的就是在项目的根目录中包含.postcssrc文件，并在其中包含以下内容：
```
{
    "plugins": {
        "autoprefixer": true
    }
}
```

我再次运行parcel index.html，检查输出的css文件，::placeholder现在有一堆浏览器前缀的样式。

> 这里有一个问题，如果把input::placeholder写在body中，在编译的css中还是没有前缀。是因为scss中，autoprefixer有另外的用法呢还是其它，待考究。

您可能会想，为什么Parcel在没有 **.babelrc** 文件的情况下使用Babel，但需要一个 **.postcssrc** 文件来处理浏览器前缀。  
这是因为Parcel已预先为具有超过1％市场份额的浏览器配置了Babel编译代码。所以只有当你需要为某些小众浏览器开发时，才需要一个.babelrc文件。

### 打包
下面，我们进行打包。
```
parcel build index.html
```
打包后，所有的文件都被压缩了。

> 这里有个问题，打包后的目录如果不更改的话，输出也在dist中，这样dist里就有了2种文件，一种是打包的，一种是非打包的。而且没有明确的命名或其它方式来做区分。如果直接上传dist目录，会导致上传的内容大于实际需要的（虽然在资源请求中不会被用到）

### 结论
最终，使用Parcel，我们只需要以下2个配置，就处理了Webpack处理的所有内容

1.  npm install node-sass;
2.  a .postcssrc file.

综上，Parcel对于加快开发过程非常棒，让我专注于构建内容而不是配置设置。而且它在编译时也比Webpack快很多！

不过，我们不应舍弃Webpack。默认情况下，Webpack尝试尽可能地扩展，而Parcel默认为您提供许多最常用的功能。快速使用是一方面，了解我们正在使用的工具做了什么也是非常重要的一方面，特别是在优化时。

## 资源
* parcel：https://parceljs.org/
* webpack -> parcel：https://logrocket.com/blog/switching-to-parcel-from-webpack/
* Other Demo：https://alligator.io/tooling/parcel/
