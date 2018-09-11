---
title: webpack不生成map文件
categories:
  - tips
tags:
  - webpack
  - uglifyjs-webpack-plugin
  - map
toc: true
date: 2018-01-24 10:51:03
---
> 问题： webpack不生成map文件

webpack中开启了map映射，但是打包一直没有map文件。  
经检查webpack的配置都没有问题，那为什么没有生成对应的map文件呢？

<!-- more -->

## 解决方案

最终找到的原因是使用uglifyjs-webpack-plugin这个插件导致的。  
在webpack的devtool[文档](https://webpack.js.org/configuration/devtool/#production)中，有一块不起眼的小字。

>When using the uglifyjs-webpack-plugin you must provide the sourceMap: true option to enable SourceMap support.

也就是当使用了uglifyjs-webpack-plugin 插件时，sourceMap这个值的默认值是false，不开启map。如果要启用map，需要在插件中配置sourceMap值为true。
```js
new webpack.optimize.UglifyJsPlugin({
    compress: {
        warnings: false,
        drop_console: true
    },
    sourceMap: true
})
```

还有一点要注意，在uglifyjs-webpack-plugin的[文档](https://github.com/webpack-contrib/uglifyjs-webpack-plugin)中指出：
>Use source maps to map error message locations to modules (This slows down the compilation) ⚠️ cheap-source-map options don't work with this plugin

使用uglifyjs-webpack-plugin了之后，devtool中就不能使用cheap-source-map这个配置了。
