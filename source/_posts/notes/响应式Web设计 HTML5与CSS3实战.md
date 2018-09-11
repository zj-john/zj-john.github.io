---
title: 响应式Web设计 HTML5与CSS3实战
categories:
  - notes
tags:
  - 响应式设计
  - flexbox
toc: true
date: 2018-08-26 11:29:07
---

![](/images/response_web.jpg)

<!-- more -->

这本书很薄，总共220页。整体上讲，和响应式相关的内容只有2章左右的内容，剩下的篇幅都在介绍新技术点，比如Flex布局、HTML5新特性、CSS3新特性及动画等。

记录一些笔记如下：

# 响应式WEB设计基础
响应式3要素：弹性布局、弹性图片、媒体查询
浏览器支持：先写一个轻量级的代码架构，然后根据所需体验针对能力更强的浏览器进行扩展，包括视觉和功能。
浏览器特性查询：[地址](https://caniuse.com/)

视口：viewport，呈现网页的区域
```html
<meta name="viewport" content="width=device-width">
```

图片：
```css
img {
  max-width: 100%;
}
```

不针对流行的设备宽度设置断点，即不绑定特定设备屏幕，而由内容和设计本身决定

在手机等小屏幕设备下改变链接、字体大小


# 媒体查询

## css
支持特性：width, height, orientation, device-width(废弃), device-height（废弃）, aspect-ratio(视口的宽高比：16/9), color, color-index, monochrome, resolution(分辨率), scan, grid
支持前缀：min, max
```css
@media screen and (min-width: 320px) {

}

```

## html
```html
<link rel="stylesheet" type="text/css" media="srceen and (orientation: portrain) and (min-width: 800px), projection" href="800wide-portrait-screen.css">
```
","号组合多个媒体查询，任何一个为真就执行。

## meta
```html
<meta name="viewport" content="maximum-scale=3,minimum-scale=0.5">
// 不可缩放
<meta name="viewport" content="initial-scale=1.0,user-scalable=no">
// more
<meta name="viewport" content="width=device-width,initial-scale=1.0">

```

## 媒体查询4级
### script
```
@media (scripting: none) {

}

@media {scripting: enabled} {

}
```

### pointer
coarse, fine, none
```
@media (pointer: coarse) {
  // 非精确指针，普通触屏
}
@media (pointer:fine) {
  // 精确指针，手写笔等
}
```

### light-level
```
@media (light-level: normal) {
  // 标准亮度
}
@media (light-level: dim) {
  // 光线较暗
}
@media (light-level: washed) {
  // 光线较强
}
```

# 弹性布局与响应式图片
将固定像素大小转换为弹性比例大小，目标/上下文

CSS自动加前缀：[Autoprefixer](https://github.com/postcss/autoprefixer)

## flexbox
```
display:flex
```
关键字： 方向、对齐、次序、弹性

### 容器
flex-direction:
flex-wrap:
flex-flow:
justify-content:主轴上元素对齐方式
align-item:交叉轴上元素对齐方式
align-content:

### Item
order: -1 位于其它元素之前
flex-grow: 空间足够的情况下，可放大的量
flex-shrink: 空间不够的情况下，可缩小的量
flex-basis: 基准
flex: flex-grow flex-shrink flex-basis
align-self:元素独立的对齐方式


## 响应式图片
```html
<picture>
  <source media="{min-width:30em}" srcset="small.jpg">
  <source media="{min-width:60em}" srcset="big.jpg">
  <img src="default.jpg" alt="pic">
</picture>
// 等同于
<img srcset="small.jpg 450w, big.jpg 900w" size="{min-width: 30em} 100vw, {min-width: 60em} 50vw" src="default.jpg" alt="pic">
```
450w 这里的w不是真实大小，只是对浏览器的一个提示，大致等于图片的“CSS像素”大小，srcset的值是浏览器自己决定选择哪一个。图片描述首先是图片名，然后是一个分辨率说明。

# HTML5与响应式Web设计
## 新增语义元素
* main
* section
* nav
* aside
* figure, figcaption (注解、图示、照片、代码)
* detail, summary （自带折叠效果）
* header
* footer
* address

## WCAG 和 WAI-ARIA
[WCAG](https://www.w3.org/WAI/standards-guidelines/wcag/glance/)  
[WAI-ARIA](https://www.w3.org/TR/2018/WD-html-aria-20180708/)

## 外部服务内嵌框架
[在线服务](http://embedresponsively.com/)，为YouTube视频、GoogleMap生成嵌入代码

# CSS3
## 响应式多列布局
```css
main {
  column-width: 12em
}
// 或
main {
  column-count: 4;
}
// 其它
main {
  column-gap:2em;
  column-rule: thin dotted #999;
}
```

```css
// default
.Item {
  display: flex;
}
// 特性查询
@support ((display: flex) and (pointer: coarse) or (transform: translate3d(0,0,0))) {
  .Item {
    display: flex;
  }
}
```

特性查询插件：[modenizr](https://modernizr.com/)

> H5之前，以数值开头的ID和类是无效的，css目前仍不允许以数值开头的选择器，但可以用属性选择器绕过

.span-class:nth-of-type(-2n+3): 每种element元素类型，单独运算

.Item:nth-child(4n+1):nth-last-child(-n+4): 等价于nth-child(4n+1) and nth-last-child(-n+4)

没有内容时的样式
.thing:empty{
  display:none
}


css自定义变量：
:root {
  --MainFont: 'Microsoft Yahei'
}

.Title {
  font-family: var(--MainFont)
}

> css属性越靠下，优先级越高

HSL：
hsl(315, 100%, 60%):
315：黄色 60，绿色120，青色 180，蓝色240， 洋红300， 红360
100%：饱和度
60%： 亮度

# CSS3高级技术
[渐变工具](http://www.colorzilla.com/gradient-editor/)
[CSS纹理](http://lea.verou.me/css3patterns/)

> 括号外的决定了页面结构，括号内的决定了页面的性能

[性能优化](https://developers.google.com/web/fundamentals/performance/rendering/)

# SVG
SVG生成工具
[svg-edit](https://editor.method.ac/)
[drawsvg](http://www.drawsvg.org)

[from-svg-to-icon](https://iconizr.com/)

svg动画
[snapsvg](http://snapsvg.io/)
[greensock](https://greensock.com)
[velocityjs](http://velocityjs.org/)

优化SVG
[svgo](https://github.com/svg/svgo)
[浏览器版svgo](https://jakearchibald.github.io/svgomg/)

svg内部样式媒体查询
```html

<style type="text/css">
  <![CDATA[
    #start_path {
      stroke: red;
    }
    // min--device-width 代替 min-width，min-width代表了svg的宽度
    @media (min--device-width: 800px) {
      #start_path {
        stroke: violet;
      }
    }
  ]]>
</style>

```

# CSS3过渡、变形和动画

过渡调速函数：[贝塞尔曲线](http://cubic-bezier.com) [调速](http://easings.net)


hack方式
```
* {
  transtition:all 1s
}
```

[matrix变形](http://www.useragentman.com/matrix/)

让老式浏览器支持H5新特性
[新特性补丁](http://afarkas.github.io/webshim/demos/)

Form CSS3
```
input:required {

}
input:required:invalid + label:after{

}
input:required:valid {

}
```

[原型工具](http://styletil.es/)
[css断点与JS联系起来](https://adactio.com/journal/5429)

> 避免在生产中使用类似bootstrap或foundation等CSS框架，1、过于冗余，2、没有美感，3、不了解原理

[代码检查工具](http://validator.w3.org/#validate_by_input)
[页面性能分析](https://www.webpagetest.org)
[HTTP2 for frontend](http://www.labazhou.net/2015/03/http2-for-front-end-web-developers/)
