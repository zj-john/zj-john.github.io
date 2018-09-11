---
title: Module not found:Can't resolve '@babel/runtime/helpers/builtin/** in '...'
categories:
  - tips
tags:
  - babel/runtime
  - npm
  - Error
toc: false
date: 2018-08-10 12:55:58
---

记录一个报错。  
很久没更新的一个项目重新启用时，报错：
```
./node_modules/_@material-ui_icons@1.1.0@@material-ui/icons/LocalCarWash.js
Module not found: Can't resolve '@babel/runtime/helpers/builtin/interopRequireDefault' in '...\node_modules\_@material-ui_icons@1.1.0@@material-ui\icons'
```
项目之前一直很稳定，怎么会突然报错呢？

看到的第一反应是：包没安装，然后马上
```
npm i @babel/runtime --save
```
然后再执行，错误依然。

这么会有这个问题呢，在项目的目录下找node_module文件夹，发现@babel/runtime/helpers下确实没有builtin目录，感觉到有可能是版本更新了。

于是看到网上有个issue中有人回答如下：
```
Everyone probably already has @babel/runtime as dependencies. Unfortunately Babel has removed the builtin helpers since v7.0.0-beta.56 (3 days ago). Since npm doesn't automatically update most people won't have this problem.

Try installing v7.0.0-beta.55, this fixes your problem:

...
    "@babel/runtime": "7.0.0-beta.55",
...
}
```
所以新版本的runtime把builtin目录拆分掉了，以至于查不到对应的目录。

所以安装以下包可以解决问题：
```
npm i @babel/runtime@7.0.0-beta.55 --save
```

# 参考：
* https://github.com/jquense/yup/issues/216
