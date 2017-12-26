---
title: Bootstrap中动态创建的元素如何添加tooltip效果
categories:
  - tips
tags:
  - tooltip
  - Bootstrap
toc: true
date: 2017-11-13 11:41:34
---
> 问题：动态创建的元素如何添加tooltip效果

tooltip是一种常用的效果类提示工具，用于增强title属性的样式。不同的前段框架有自己不同的使用和展现方式，比如[Bootstrap](http://getbootstrap.com/docs/4.0/components/tooltips/)、[JQueryUI](https://jqueryui.com/tooltip/)、[Popper](https://popper.js.org/index.html)等。本文主要依托Bootstrap讲解。

对于Bootstrap，只需要如下代码即可完成配置。
```html
<!-- HTML中如下配置： -->
<!-- placement：top（default） right bottom left -->
<button type="button" data-toggle="tooltip" data-placement="top" title="Tooltip on top">
  Tooltip on top
</button>
<!-- title可以加html样式，如下： -->
<button type="button" data-toggle="tooltip" data-html="true" title="<em>Tooltip</em> <u>with</u> <b>HTML</b>">
  Tooltip with HTML
</button>
```

```javascript
// js中触发
$('button').tooltip();
```
这种显示的配置适用于页面初始化。如果一个元素是动态生成的，比如说datatable column中生成，这种方式将会失效。
<!-- more -->

# 解决方案
使用selector属性：
```JavaScript
$('body').tooltip({
    selector: '[rel=tooltip]'
});
```

官方说明如下；
| Name     |       Type      | Default | Description                                                                                                                                                             |
|----------|:---------------:|--------:|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| selector | string or false |   false | If a selector is provided, tooltip objects will be delegated to the specified targets. In practice, this is used to enable dynamic HTML content to have popovers added. |
