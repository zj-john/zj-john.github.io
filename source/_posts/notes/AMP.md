---
title: AMP
categories:
  - notes
tags:
  - AMP
toc: true
date: 2018-09-01 11:13:51
---

AMP(Accelerated Mobile Pages)技术

<!-- more -->

## WHY
加速页面 + 集成标准 + SEO

* 支持：google、百度、搜狗、Yahoo（Japan）

* 国内：MIP（Mobile Instant Page）

* 标示：

![](/images/amp_flag.png)

## 主要技术
1. AMP HTML(核心)  

AMP HTML在图像显示等方面使用与HTML不同的专用标签，另外还限制了HTML部分功能的使用 ，后缀名.html 或 .amp.html

2. AMP JS：

JS库，保证AMP HTML的正确和快速显示。除此之外，AMP JS还负责在只支持普通HTML的浏览器中担任桥梁，使其能正确支持AMP HTML的专用功能。AMP HTML中可以调用该函数库

3. AMP Cache：

是缓存并传输AMP页面的CDN，进一步提高AMP网页的性能。用户在搜索引擎中点击AMP网页时，实际上访问的是优化后的缓存页面。Google的AMP Cache名为Google AMP Cache。


## 内容

* &lt;html&gt; - >  &lt;html amp&gt;

* &lt;head&gt;标签：charset（必须）、viewport元标签（必须），格式固定的&lt;style&gt;元素，以及读取AMP JS库的&lt;script&gt;元素等（其中一部分顺序也有规定）、meta、css（必须内联、不能外部、不能style，不能important，不能*，增加attr：layout=“responsive”，&lt;style amp-custom&gt;）

* 自定义元素：&lt;amp-img&gt;，&lt;amp-sidebar&gt;，&lt;amp-facebook&gt;，&lt;amp-ad&gt;，&lt;amp-analytics&gt;  选择分辨率+自动延迟加载

* 脚本：原则上任何第三方js不能使用，用自定义组件（内置组件和扩展组件）替换（但可用，不能通过验证，实际无效）

![](/images/amp_basic.png)

## 验证
* 验证meta、viewport 等标签
* 验证标签属性，如：charset, rel, amp等
* 不允许引入第三方的script标签

## 个人观点
1.AMP是集成解决方案，适用于从0开始，或易于改造的项目，对于交互和设计强的应用，成本过高（开发新组件、重构）

2.技术是：cdn(免费) + 预加载 + 组件优化，除预加载外，其余都可以在现有技术下做掉或已经做掉。

3.先检测下当前页面可优化的空间


## 总结：

新页面：业务功能 评估

老页面：成本高（HTML重构 + 重新布局 + 交互有限）VS  SEO评估


> 目前AMP提供共80+内置和扩展组件，基本覆盖了一般页面的需求（增长速度比较快，社区活跃）

> 目前可以使用的交互方式是，页面上可以通过AMP中的bind组件，设置一些变量，通过对变量的赋值及简单操作，改变变量值，来实现组件的显示、隐藏，达到交互效果
