---
title: Chrome devtools-Performance面板-技巧
categories:
  - notes
tags:
  - note
  - Chrome
  - devtools
toc: true
date: 2018-01-02 12:07:06
---
chrome大概是最常用的浏览器之一，这篇笔记讲解下Chrome devtools中Performance面板的具体用法。  

[官方教程](https://developers.google.com/web/tools/chrome-devtools/)

<!-- more -->

# Performance面板
## 分析运行时性能

* 不要编写会强制浏览器重新计算布局的 JavaScript。将读取和写入功能分开，并首先执行读取。
* 不要使您的 CSS 过于复杂。减少使用 CSS 并保持 CSS 选择器简洁。
* 尽可能地避免布局。选择根本不会触发布局的 CSS。
* 绘制比任何其他渲染活动花费的时间都要多。请留意绘制瓶颈。


### JavaScript
JavaScript 计算，特别是会触发大量视觉变化的计算会降低应用性能。 不要让时机不当或长时间运行的 JavaScript 影响用户交互。

#### 工具
* 进行 Timeline 记录，并找出疑似较长的 Evaluate Script 事件（<b>黄色</b>)
* 如果JavaScript 中出现较多的卡顿现象，您可能需要进一步分析并收集 JavaScript CPU 配置文件。CPU 配置文件会显示执行时间花费在页面的哪些函数上

#### 问题
| 问题        | 示例           | 解决方案  |
| ------------- | ------------- | ----- |
| 大开销输入处理程序影响响应或动画。 | 触摸、视差滚动。 | 	让浏览器尽可能晚地处理触摸和滚动，或者绑定侦听器 |
| 时机不当的 JavaScript 影响响应、动画、加载。 | 页面加载后用户向右滚动、setTimeout/setInterval。      |   优化 JavaScript 执行：使用 [requestAnimationFrame](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/requestAnimationFrame)、使 DOM 操作遍布各个帧、使用网络工作线程 |
| 长时间运行的 JavaScript 影响响应。 | DOMContentLoaded 事件由于 JS 工作过多而停止      | 将纯粹的计算工作转移到网络工作线程。如果您需要 DOM 访问权限，请使用 requestAnimationFrame |
| 会产生垃圾的脚本影响响应或动画。 | 任何地方都可能发生垃圾回收。      | 减少编写会产生垃圾的脚本 |


### 样式
样式更改开销较大，在这些更改会影响 DOM 中的多个元素时更是如此。 只要您将样式应用到元素，浏览器就必须确定对所有相关元素的影响、重新计算布局并重新绘制。
#### 工具
* 进行 Timeline 记录。检查大型 Recalculate Style 事件（<b>紫色</b>）

#### 问题
| 问题        | 示例           | 解决方案  |
| ------------- | ------------- | ----- |
| 大开销样式计算影响响应或动画。 | 任何会更改元素几何形状的 CSS 属性，如宽度、高度或位置；浏览器必须检查所有其他元素并重做布局。 | 	避免会触发布局的 CSS。 |
| 复杂的选择器影响响应或动画。 | 嵌套选择器强制浏览器了解与所有其他元素有关的全部内容，包括父级和子级。 | 	在 CSS 中引用只有一个类的元素（高级的选择器可能消耗大量的计算时间）。 |


### 布局
布局是浏览器用来计算页面上所有元素的位置和大小的过程

#### 工具
Timeline 可以确定页面何时会导致强制同步布局 (<b>红色</b>竖线)

#### 问题
 | 问题        | 示例           | 解决方案  |
 | ------------- | ------------- | ----- |
 | 强制同步布局影响响应或动画。 | 强制浏览器在像素管道中过早执行布局，导致在渲染流程中重复步骤。 | 先批处理您的样式读取，然后处理任何写入 |
 | 布局抖动影响响应或动画。 | 形成一个使浏览器进入读取-写入-读取写入周期的循环，强制浏览器反复地重新计算布局。 | 	使用 [FastDom](https://github.com/wilsonpage/fastdom) 内容库自动批处理读取-写入操作 |

### 绘制合成
绘制是填充像素的过程。这经常是渲染流程开销最大的部分。 如果您在任何情况下注意到页面出现卡顿现象，很有可能存在绘制问题。

合成是将页面的已绘制部分放在一起以在屏幕上显示的过程。

#### 工具
Summary中Painting中耗时长

#### 问题

| 问题        | 示例           | 解决方案  |
| ------------- | ------------- | ----- |
| 绘制风暴影响响应或动画。 | 较大的绘制区域或大开销绘制影响响应或动画。 | 避免绘制、提升将要移动到自有层的元素，使用变形和不透明度 |
| 层数激增影响动画。 | 使用 translateZ(0) 过度提升过多的元素会严重影响动画性能。 | 	请谨慎提升到层，并且仅在您了解这样会有切实改进时提升到层 |


## 时间线事件属性参考
[文档](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance/performance-reference)
### 时间线事件属性
|属性	|显示时间|
| ------------- | ------------- |
|Aggregated time	|对于带嵌套事件的事件，每个类别的事件所用的时间。|
|Call Stack	|对于带子事件的事件，每个类别的事件所用的时间。|
|CPU time	|记录的事件所花费的 CPU 时间。
|Details	|有关事件的其他详细信息。
|Duration (at time-stamp)|	事件及其所有子事件完成所需的时间，时间戳是事件发生的时间（相对于记录开始的时间）。
|Self time	|事件（不包括任何子事件）花费的时间。
|Used Heap Size|	记录事件时应用使用的内存大小，以及自上次采样以来已使用堆大小的增减 (+/-) 变化。

### loading事件
属于加载类别的事件及其属性
#### 事件
* Parse HTML：Chrome 执行其 HTML 解析算法。
* Finish Loading：	网络请求已完成。
* Receive Data：	请求的数据已被接收。存在一个或多个 Receive Data 事件。
* Receive Response：	请求的初始 HTTP 响应。
* Send Request：	网络请求已被发送。

#### 属性
|属性	|说明|
| ------------- | ------------- |
|Resource|	请求的资源的网址。|
|Preview	|请求的资源的预览（仅图像）。
|Request Method	|用于请求的 HTTP 方法（例如，GET 或 POST）。
|Status Code	|HTTP 响应代码。
|MIME Type	|请求的资源的 MIME 类型。
|Encoded Data Length	|请求的资源的长度（以字节为单位）。


### Scripting 事件
属于脚本类别的事件及其属性
#### 事件
* Animation Frame Fired：预定的动画帧被触发，其回调处理程序被调用。
* Cancel Animation Frame：	预定的动画帧被取消。
* GC Event：	发生垃圾回收。
* DOMContentLoaded：	浏览器触发 DOMContentLoaded。当页面的所有 DOM 内容都已加载和解析时，将触发此事件。
* Evaluate Script：	脚本已被评估。
* Event：	JavaScript 事件（例如，“mousedown”或“key”）。
* Function Call：	发生顶级 JavaScript 函数调用（只有浏览器进入 JavaScript 引擎时才会出现）。
* Install Timer：	已使用 setInterval() 或 setTimeout() 创建定时器。
* Request Animation Frame：	requestAnimationFrame() 调用已预定一个新帧。
* Remove Timer：	之前创建的定时器已被清除。
* Time：	一个脚本调用了 console.time()
* Time End：	一个脚本调用了 console.timeEnd()
* Timer Fired：	使用 setInterval() 或 setTimeout() 创建的定时器已被触发。
* XHR Ready State Change：	XMLHTTPRequest 的就绪状态已发生变化。
* XHR Load：	XMLHTTPRequest 已结束加载。

#### 属性
|属性	|说明|
| ------------- | ------------- |
|Timer ID|	定时器 ID。|
|Timeout|	定时器指定的超时。|
|Repeats	|指定定时器是否重复的布尔值。|
|Function Call	|已调用一个函数。|

### Rendering 事件
渲染类别的事件及其属性
#### 事件
* Invalidate layout：页面布局被 DOM 更改声明为无效。
* Layout：	页面布局已被执行。
* Recalculate style：	Chrome 重新计算了元素样式。
* Scroll：	嵌套视图的内容被滚动。

#### 属性

|属性	|说明|
| ------------- | ------------- |
|Layout invalidated	|对于 Layout 记录，导致布局失效的代码的堆叠追踪。|
|Nodes that need layout	|对于 Layout 记录，被标记为需要在重新布局启动前布局的节点的数量。正常情况下，这些代码是被开发者代码声明为无效的代码，以及向上追溯到重新布局根目录的路径。|
|Layout tree size	|对于布局记录，重新布局根目录下节点（Chrome 启动重新布局的节点）的总数。|
|Layout scope	|可能的值为“Partial”（重新布局边界是 DOM 的一部分）或“Whole document”。|
|Elements affected	|对于 Recalculate 样式记录，受样式重新计算影响的元素的数量。|
|Styles invalidated	|对于 Recalculate 样式记录，提供导致样式失效的代码的堆叠追踪。|


### Painting 事件
打印类别的事件及其属性
#### 事件
* Composite Layers：	Chrome 的渲染引擎合成了图像层。
* Image Decode：	一个图像资源被解码。
* Image Resize：	一个图像的大小相对于其原生尺寸发生了变化。
* Paint：	合成的图层被绘制到显示画面的一个区域。将鼠标悬停到 Paint 记录上会突出显示已被更新的显示画面区域。

#### 属性
|属性	|说明|
| ------------- | ------------- |
|Location	|对于 Paint 事件，绘制矩形的 x 和 y 坐标。|
|Dimensions	|对于 Paint 事件，已绘制区域的高度和宽度。|
