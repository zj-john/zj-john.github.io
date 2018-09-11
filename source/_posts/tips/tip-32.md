---
title: angular 关闭hash url
categories:
  - tips
tags:
  - angular
  - hash
  - route
toc: true
date: 2018-01-17 14:03:01
---
> 问题：angular 关闭hash url

未配置的angular1中route路由（用的是$stateProvider）是使用hash路由的，也就是使用http://localhost/#/index 这种方式来访问。
如果要恢复http://localhost/index 这种方式的话，做法如下

<!-- more -->

## 解决方案
1. 第一步：启用HTML5Mode
```js
app.config(["$locationProvider", function($locationProvider) {
    $locationProvider.html5Mode(true);
}]);
```

2. 第二步：在入口html文件中设置Root URL
```html
<head>
  ...
  <base href="/">
  ...
</head>
```
