---
title: angular checkbox 取值技巧
categories:
  - tips
tags:
  - angular
  - checkbox
toc: true
date: 2017-11-10 11:12:56
---
> 问题：angular checkbox 取值技巧

angular中使用checkbox，绑定model后，返回的默认值是bool类型的true or false。这对于对于需要特定返回的情况，处理不太友好。

<!-- more -->

# 解决方案
使用ng-true-value和ng-false-value,这两个值分别指定选中和不选中checkbox时 ng-model的值。
```html
<input type="checkbox" ng-model='test' ng-true-value='1'  ng-false-value="0" name="type" >
```
