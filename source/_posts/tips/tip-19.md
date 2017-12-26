---
title: react 利用根目录进行引用组件
categories:
  - tips
tags:
  - react
  - webpack
toc: true
date: 2017-12-05 12:25:12
---
> 问题：react 利用根目录进行引用组件

react中由于使用node的引用方式，所以经常会跨各种文件夹引用，比如：
```
import ActionWizard from "../../../components/forms/wizards/ActionWizard";
import UiValidate from "../../../components/forms/validation/UiValidate";
```
这种方式写起来和修改起来相当麻烦。

<!-- more -->

# 解决方法
使用webpack中的alias方法配置别名：
```
const path = require('path');

module.exports = {
 ...
 resolve: {
    alias: {
      'ComponentsRoot': path.resolve(__dirname, './src/app/components')
    }
},
 ...
};
```

然后在引用时，方式如下：
```
import ActionWizard from "ComponentsRoot/forms/wizards/ActionWizard";
import UiValidate from ".ComponentsRoot/forms/validation/UiValidate";
```
