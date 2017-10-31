---
title: chrome button按钮点击之后会有蓝色边框
categories:
  - tips
tags:
  - chrome
  - button
  - outline
date: 2017-10-09 15:08:58
toc: true
---
> 问题： chrome button按钮点击之后会有蓝色边框

为了更好的用户体验，chrome浏览器默认会为当前获得焦点的button添加outline属性（一个带颜色的边框），这样可以方便用户使用tab键更换当前焦点按钮。怎么去除这种效果呢？

![蓝色边框](/images/button-outline.jpg)

<!-- more -->

# 解决方法
如果想要去掉这种效果，可以在css中添加样式
```Javascript
button:focus {outline:none;}
//或者
button:focus {outline:0;}
```
>其它浏览器，比如Firefox也会有类似的行为.如果想要去除，需要使用Mozilla的私有属性：
```Javascript
::-moz-focus-inner {border-color: transparent;}
// 或
::-moz-focus-inner {border:0;}
```


另外：此种方式对初始化的元素生效，对js中创建的元素是否生效待测试
