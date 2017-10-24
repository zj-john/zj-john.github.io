---
title: img src 默认地址设置
categories:
  - tips
tags:
  - img
  - default
  - onerror
  - react
date: 2017-09-19 15:05:02
---
> 问题：img src 地址不存在时，展示默认图片

<!-- more -->

解决方案如下：
```javascript
//js
//this.onerror='' 防止默认图片失效导致死循环
<img src="image.gif" onerror='this.onerror="";this.src="default.jpg"'/>

//react
<img src={this.state.img} ref={img => this.img = img} onError={
    () => this.img.src = 'img/default.img'
}>
```

>tips: onerror 事件会在文档或图像加载过程中发生错误时被触发。  
支持该事件的 HTML 标签:&lt;img&gt;, &lt;object&gt;(添加多媒体时使用), &lt;style&gt;。多用于img。
