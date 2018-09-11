---
title: 使用了babel但报错object.assign is not a function
categories:
  - tips
tags:
  - react
  - babel
toc: true
date: 2018-01-26 11:27:44
---
> 问题： babel不转换object.assign方法

react中用到object.assign，虽然安装了babel，但是webpack打包后还是在浏览器报object.assign is not a function。

<!-- more -->

## 解决方案
object.assign是ES6的中的方法，按照原先的理解，我安装了babel之后，应该就可以把ES6的语法转换掉。  
但是在babel中[测试](https://babeljs.io/repl/),发现确实object.assign不会做任何转换。

查了下，阮大神的[解释](http://www.ruanyifeng.com/blog/2016/01/babel.html)最为透彻

引用如下：  
Babel默认只转换新的JavaScript句法（syntax），而不转换新的API，比如Iterator、Generator、Set、Maps、Proxy、Reflect、Symbol、Promise等全局对象，以及一些定义在全局对象上的方法（比如Object.assign）都不会转码。  
举例来说，ES6在Array对象上新增了Array.from方法。Babel就不会转码这个方法。如果想让这个方法运行，必须使用babel-polyfill，为当前环境提供一个polyfill。

```
npm install --save babel-polyfill
```

```
import 'babel-polyfill';
```

以下文件中的方法是babel不转换的，需要[polyfill](https://babeljs.io/docs/usage/polyfill/)来帮助
[babel-plugin-transform-runtime](https://github.com/babel/babel/blob/master/packages/babel-plugin-transform-runtime/src/definitions.js)

> babel-polyfill includes a custom regenerator runtime and core-js. 所以直接使用core-js也可以达到相同的转换效果
