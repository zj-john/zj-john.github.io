---
title: 调试hover效果，如tooltip等
categories:
  - tips
tags:
  - hover
  - 调试
  - tooltip
  -
toc: false
date: 2018-10-19 15:30:26
---


在chrome上调试hover效果是一个很麻烦的事情，因为鼠标移动，hover的效果（dom结构）就没了，怎么保持住hover效果，然后调试dom呢？

方法是:
在页面上要调试的元素上，hover后，右键，选择“检查”，
![](/images/hoverDebugTool.png)

此时，就可以在dom结构中调试hover出来的dom结构了。
![](/images/hoverDebug.png)

> 打开开发者工具时，尽量让开发者窗口贴着要调试的元素。因为离得远的话，很可能把鼠标移动到dom结构之前，就触发了其它的页面效果，导致希望的hover效果丢失。
