---
title: react中import css文件全局污染
categories:
  - tips
tags:
  - CSS Module
  - react
toc: true
date: 2018-01-09 11:27:30
---
> 问题：react中import css文件全局污染

react中利用webpack打包，可以把css作为一个model导入到文件中，方式如下：
```
import './TestModel.css'
```
不过这种方式进来的css文件实际上是在渲染的时候，给对应的html中添加了一个内嵌样式&lt;style&gt;,那么如果一个html是由多个component组成，而每个component中都import了一个css文件，那么这个html文件在实际加载时，就会把多个component中的CSS文件作为&lt;style&gt;同时加载，这么一来就把这些CSS的作用域放大化了，就会造成样式污染。

<!-- more -->

## 解决方案

参考腾讯团队的这篇[文章](http://www.alloyteam.com/2017/03/getting-started-with-css-modules-and-react-in-practice/)

![](/images/css_module.png)

一般做法：
* 加个父元素的选择器，限制范围
* Class的名称复杂一些，降低冲突概率（主要是和外部引用插件之间的冲突）
* 保证所有CSS不重名，copy的文件先重命名……

### 命名约定
这种方式大概最容易想到，既然CSS变为全局化，那么直接把它们写在一个文件中就可以了。如果真的写在了一个文件中，那么怎么区分各个部分呢，就要依靠一个规范化的命名约定。

CSS应该如何组织和命名，可以参考[BEM](http://getbem.com/naming/),更多信息可以搜索 [OOCSS（面向对象CSS）](http://oocss.org/)，[SMACSS](https://amcss.github.io/)，[SMACSS](https://smacss.com/)，[SUITCSS](https://suitcss.github.io/)


缺点：
* JS CSS之间没有打通(复杂组件使用JS和CSS共同处理样式，无法打通造成冗余)
* 命名复杂化

### JS中直接使用style
抛弃CSS文件，直接在JS中定义style的object对象，然后内联方式使用。

缺点：
* 无法使用伪类
* 样式会大量重复
* 不能使用stylus、sass等成熟的预处理器


### JS管理模块
最常用[CSS Modules](https://github.com/css-modules/css-modules)，可以依托于构建工具使用，比如[webpack](https://doc.webpack-china.org/loaders/css-loader/#modules)。

webpack中使用方法：
webpack.config
```js
{
  test: /\.css$/,
  use: [
    {
      loader: 'css-loader',
      options: {
        // 启用
        modules: true,
        // 最终生成的样式
        localIdentName: '[name]__[local]--[hash:base64:5]'
      }
    }
  ]
}

```

* 语法 :local(.className) 可以被用来在局部作用域中声明 className。局部的作用域标识符会以模块形式暴露出去(即使用时需要导入各个模块)。

* 使用 :local（无括号）可以为此选择器启用局部模式。:global(.className) 可以用来声明一个明确的全局选择器。使用:global（无括号）可以将此选择器切换至全局模式。

* loader 会用唯一的标识符(identifier)来替换局部选择器。所选择的唯一标识符以模块形式暴露出去。


demo.css
```css
.normal {
  color: green;
}
:local(.className) { background: red; }
:local(.className .subClass) { color: green; }
:local .className .subClass :global(.global-class-name) { color: blue; }
```

> 你可以使用 :local(#someId)，但不推荐这种用法。推荐使用 class 代替 id。

demo.js
```js
import styles from './demo.css';
console.log(styles);
buttonElem.outerHTML = `<button class=${styles.normal}>Submit</button>`
```

生成的HTML：
```
<button class="button--normal-abc53">Submit</button>
```
缺点：
1. 需要在运行时才获取className，性能损耗大。  
解决： （[babel-plugin-react-css-modules](https://github.com/gajus/babel-plugin-react-css-modules)），【未测试】
2. 对于嵌套和外部组件样式更改无效，比如：  
嵌套：
```
.menu > .btn > .fa {
    position: relative;
    bottom: 4px;
}
```
外部样式：下面是修改datatable渲染后的默认样式的
```
ul.dt-button-collection>.active>a,
ul.dt-button-collection>.active>a:focus,
ul.dt-button-collection>.active>a:hover {
  color: black;
  background-color: white;
}
```

### 当前方案

鉴于以上问题和改造的成本，目前项目中采用的还是最原始的“加个父元素的选择器，限制范围”方案，此种方案后期改为less或sass，直接提取父元素加一个嵌套即可。
