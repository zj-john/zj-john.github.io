---
title: 生产环境disable console log
categories:
  - tips
tags:
  - console
  - production
toc: true
date: 2017-12-13 15:08:55
---
> 问题：生产环境disable console log

开发和测试环境中会用到大量console.log方法来进行代码的调试，但生产上需要关闭这些输出。  
有以下几种方法。

<!-- more -->

# Hack方法
这应该是最容易想到的方法，直接改写console.log的方法。
```javascript
if (!DEBUG_MODE_ON) {
    console = console || {};
    console.log = function(){};
}
```


# DEBUG 参数控制
这种方式比较灵活，不过对于新接入的应用需要来一次全局的替换。
```javascript
// before
console.log("output");
// after
DEBUG && console.log("output");
```

# webpack等构件工具中配置

以webpack为例，
使用[UglifyJsPlugin](https://github.com/webpack-contrib/uglifyjs-webpack-plugin)插件

```javascript
[
  new UglifyJsPlugin({
    uglifyOptions: {      
      compress: {
          // 在这里处理
          drop_console: true
      }
    }
  })
]
```

其它的compress options的取值[点此](https://github.com/mishoo/UglifyJS2/tree/harmony#compress-options)
