---
title: clean-webpack-plugin :clean from outside of the project root
categories:
  - tips
tags:
  - webpack
  - clean-webpack-plugin
  - allowExternal
date: 2017-09-18 11:17:34
toc: true
---
> 问题：clean-webpack-plugin :clean from outside of the project root

<!-- more -->

webpack中的clean-webpack-plugin插件一般用于在打包前删除打包(dist)目录。  
``` Javascript
//install
npm install clean-webpack-plugin --save-dev

// usage
new CleanWebpackPlugin(paths [, {options}])

// webpack.config.js 中
var CleanWebpackPlugin = require('clean-webpack-plugin');

module.exports = {
  plugins: [
    //  要删除的path：dist build
    new CleanWebpackPlugin(['dist', 'build'], {
      // An absolute path for the root
      root: '/full/project/path',
      // Write logs to console.
      verbose: true,
      // Use boolean "true" to test/emulate delete. (will not remove files).
      // Default: "false", remove files
      dry: false,
      // Instead of removing whole path recursively,
      // remove all path's content with exclusion of provided immediate children.
      // Good for not removing shared files from build directories.
      exclude: ['shared.js']
    })
  ]
}


```
在webpack使用clean-webpack-plugin插件时，如果指明删除的path(["dist","build"])是webpack所在目录的上级或上级其它目录时，会提示不在项目的root范围内，无法删除。这是一种防止误操作的保护机制。  
如果打包目录不在前端的文件夹下，比如和java结合，打包目录在java static下，此时需要指定root，才可以删除。  
具体如下：

#### 方案1   
指定root根目录解决outside的问题，但root需要用绝对地址，所以用resolve解决掉路径中的".."或"."
```
new CleanWebpackPlugin(['*.*', 'js'], {
    root: path.resolve(__dirname, '../main/resources/static'),
    verbose: true
})
```


#### 方案2
通过查看clean-webpack-plugin的[源码](https://github.com/johnagan/clean-webpack-plugin/blob/master/index.js)，可以找到 allowExternal 这个参数。
```
// 判断是否在project目录下的逻辑：
// disallow deletion any directories outside of root path.
if (rimrafPath.indexOf(projectRootDir) < 0 && !_this.options.allowExternal) {
  _this.options.verbose && console.warn(
    'clean-webpack-plugin: ' + rimrafPath + ' is outside of the project root. Skipping...');
  results.push({ path: rimrafPath, output: 'must be inside the project root' });
  return;
}
```
设置allowExternal: true即可满足需求。默认是false，如下：
```
options.allowExternal = options.allowExternal || false;
```

> Tips/Questions：clean-webpack-plugin中使用的删除插件是node的rimraf,功能相当于rm -rf(带递归删除)。但是在实际测试中，如果要删除的目录不是空目录，会把当前目录下文件删除后，报错不能删除非空文件夹。 具体原因还没深究，有知晓的童鞋一起分享下，谢谢。
