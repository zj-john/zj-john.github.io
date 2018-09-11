---
title: JS 物理引擎
categories:
  - tips
tags:
  - box2d
toc: false
date: 2018-05-23 15:32:14
---

最近在做一个canvas的东西，看的过程中，顺便了解下JS的物理引擎。  
微信上目前有很多很火的小程序，跳格子、弹球等，都是一些物理学游戏，其中很重要的部分是如何模拟一个真实的物理世界，这就用到了物理引擎。

<!-- more -->

我最早知道的物理引擎是box2dJS，看过demo，但一直没有了解过。

现在重拾看看，先记录下以下资料。

按照Stack Overflow上推荐的答案(截止2017.04)，目前有如下引擎库。

基于[Box2D](https://github.com/erincatto/Box2D)：

* [kripken/box2d.js](https://github.com/kripken/box2d.js)  

* [planck.js](https://github.com/shakiba/planck.js)   

* [Box2DJS](http://box2d-js.sourceforge.net/)  

* [box2dweb](http://code.google.com/p/box2dweb/)   


不基于Box2D:

* [Matter.js](http://brm.io/matter-js/)

* [P2.js](https://github.com/schteppe/p2.js)

比较推荐的是kripken/box2d.js和planck.js，box2dweb优于Box2DJS。

> kripken/box2d.js基于[Emscripten](http://www.ruanyifeng.com/blog/2017/09/asmjs_emscripten.html)，虽然效率和性能提高，但是开发的成本提高。




参考文档：  

1. https://stackoverflow.com/questions/7628078/which-box2d-javascript-library-should-i-use
