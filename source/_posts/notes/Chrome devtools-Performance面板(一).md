---
title: Chrome devtools-Performance面板-初步使用[译]
categories:
  - notes
tags:
  - note
  - Chrome
  - devtools
toc: true
date: 2018-01-01 17:57:21
---
chrome大概是最常用的浏览器之一，这篇笔记讲解下Chrome devtools中Performance面板的具体用法。  

[官方教程](https://developers.google.com/web/tools/chrome-devtools/)

<!-- more -->

# Performance面板
使用性能面板可以通过分析运行时的性能。
翻译自[Get Started With Analyzing Runtime Performance](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance/?refresh=1&pli=1)

## 开始
1. 请在隐身模式打开Google Chrome
2. URL打开：https://googlechrome.github.io/devtools-samples/jank/
3. 打开DevTools

>隐身模式可以确保Chrome运行在一个干净的状态，便于分析网页的性能。如果你安装了大量的扩展插件,这些插件可能会产生影响性能分析的噪声。

![](/images/get-started.png)

### 模拟移动设备CPU
移动设备比台式机和笔记本电脑有更少的CPU。所以当你分析页面时,使用CPU Throtting 来模拟页面在移动设备上的执行
1. 在DevTools,单击Performance面板
2. 确保Screenshots复选框启用
3. 单击Capture Settings。
4. 对于CPU选项,选择2x slowdown。DevTools将会使用比平时慢2倍的CPU运行页面。

![](/images/throttling.svg)
>不同设备不同配置的设备需要设置不同的CPU放慢速度


### DEMO演示
由于读者配置、设备等不同，很难创建一个对所有读者来说一致的运行性能演示。所以在本DEMO（打开URL的DEMO）中，设置了一种可以自动调节的机制来满足不同读者的性能演示。
1. 持续点击"Add 10",直到蓝色方块移动明显比以前慢。在高端机器上,可能需要大约20点击
2. 点击"Optimize",蓝色方块的动画会变得更快更顺利。
3. 单击"Un-Optimize",蓝色方块移动会变慢有闪烁的感觉。

### 记录运行时的状态
为什么优化后效果会变得更好呢，我们通过performance面板来分析下。
1. Performance面板中点击"记录"，DevTools会捕获在页面运行时性能指标
2. 等几秒钟
3. 单击停止记录，DevTools会停止记录并处理数据,然后显示性能分析结果。

![](/images/profiling.png)
![](/images/results.png)

## 分析结果

### 分析FPS
FPS:frames per second   

其中一个测量性能的主要指标是每秒的动画帧数(FPS)，对于动画来说，FPS === 60会是一个好的体验，少于这个数会觉得卡顿，大于这个数会觉得太快。
#### FPS图表
如果FPS图表上是一片红色柱子，说明帧速率过低可能影响用户体验。通常来说，绿色越多FPS越高。

![](/images/fps-chart.svg)


#### CPU图表
FPS图表下是CPU图表，CPU图表中的颜色说明和Summary部分一致。如果CPU图中填满了颜色，说明cpu在性能记录过程中一直被占满了，此时应该想办法优化。

![](/images/cpu-summary.svg)

#### 网页截图
当鼠标在FPS、CPU或者NET图上划过时，可以看到对应时刻的网页截图，用以结合分析动画性能。

![](/images/screenshot.png)

#### 悬停
在Frame部分，可以通过悬停鼠标来看这一时刻的FPS

![](/images/frame.png)

> 打开FPS监控盘：
1. 使用快捷键 ctrl+shift+P (win) 或者Command+shift+P(mac) 来打开名命令菜单
2. 输入Rendering，选择Show Rendering
3. 在Rendering窗格，启动FPS Meter。  

![](/images/fps-meter.png)

### 查找瓶颈

#### Summary选项卡  
当不点击任何具体event时，显示的是整个记录行为中各个分类所占的时间。对这个case来说，可以看到Rendering占了大部分时间，所以最好的优化方式是减少这一部分时间。

![](/images/summary.svg)

#### 展开Main部分  
这一部分展示了主线程的event图。X轴记录了记录的时间，每个小柱图代表一个event，柱子的宽度越大表示这个event占用的时间越多；Y轴代表调用栈，如果同一Y轴上有多个events,说明靠上的event调用了下方的event。

![](/images/main.svg)

#### 查找具体数据  
通过在时间轴上点击，鼠标滚轮来放大一个单一的 Animation Frame Fired Event.此时Summary显示的这个event的具体信息。

> 放大事件的另一个方法是点击后利用W A S D键来操作。W,S是放大、缩小；A,D是移动.

![](/images/zoomed.png)

#### 帧的右上角标识  
如果在Event的右上角看到一个红色的三角，说明这个Event可能有些问题。

![](/images/triangle.png)

> 当 requestAnimationFrame() 函数回调时会触发Animation Frame Fired Event

#### 定位Event  
点击 Animation Frame Fired Event,Summary窗格上会展示这个event的详细信息，注意里面有一个"reveal"的链接。单击这个链接可以显示这个event的具体调用地址。再次单击可以跳转到源代码中具体的行内。

![](/images/animation-frame-fired.png)

#### 进入代码
在app.update event下，如果放大看，可以看到下面的每一个紫色的event右上都有一个红色的三角。点击其中的一个查看Summary的具体信息。实际上，每一个都表示有一次强制重构布局。

![](/images/zoomed.png)

在上面的紫色event上点击具体的调用源代码地址 app.js:70,这行代码就是导致重构布局的原因。

>这段代码的问题是,在每个动画帧,它改变了每个方块的style,然后查询每个方块在页面上的位置。因为style改变了,浏览器不知道每个方块的具体位置,所以它对方块进行重新布局以计算其位置。 [More](https://developers.google.com/web/fundamentals/performance/rendering/avoid-large-complex-layouts-and-layout-thrashing#avoid_forced_synchronous_layouts)

![](/images/forced-layout-src.png)
