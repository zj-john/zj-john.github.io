---
title: import中{}加与不加的区别
categories:
  - tips
tags:
  - import
toc: true
date: 2018-01-04 11:49:32
---
> 问题：import中{}加与不加的区别

import模块时，有时有{}，有时不加，区别是什么呢？
```
import { Tooltip } from "react-bootstrap";
import ActionModal from "ComponentRoot/modals/ActionModal";
```
<!-- more -->

## 实现原理

[官网](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/import)

一般import时有以下几种方式：
```
// 导出默认模块
import defaultExport from "module-name";

// 导出所有模块，并且指定"name"为命名空间，使用时module-name.someFunc() -> name.someFunc();
import * as name from "module-name";

// 导出 module-name 中 export 模块
import { export } from "module-name";

// 以别名导出
import { export as alias } from "module-name";

// 导出 module-name 中 export1, export2 模块 (muti)
import { export1 , export2 } from "module-name";

// 综合导出
import { export1 , export2 as alias2 , [...] } from "module-name";
import defaultExport, { export [ , [...] ] } from "module-name";

// 在这种情况下，默认导入将必须首先声明
import defaultExport, * as name from "module-name";

// 等价于 import * from "module-name" 以module-name.someFunc()方式使用
import "module-name";
```
