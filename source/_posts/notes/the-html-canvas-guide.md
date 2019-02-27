---
title: The HTML Canvas Guide
categories:
  - notes
tags:
  - JavaScript Weekly
  - html
  - canvas
  - 2018.11.09
toc: true
date: 2018-11-14 12:58:02
---



![Banner](/images/JS_Weekly/the-html-canvas-guide/banner.png)

本文介绍一种可以在浏览器屏幕上绘画的方式，即Canvas API。
在HTML中，canvas是一个标签：`<canvas>`，标识可以通过Canvas API绘画的元素。

<!-- more -->

## 新建canvas
新建Canvas很简单，只要把`<canvas></canvas>`放到HTML文件中即可。

![](/images/JS_Weekly/the-html-canvas-guide/Screen Shot 2018-10-28 at 09.23.22.png)

此时，你看不到任何内容，因为canvas是一个不可见的元素。让我们添加下边框。

![](/images/JS_Weekly/the-html-canvas-guide/Screen Shot 2018-10-28 at 09.23.43.png)

因为Chrome中会自动给`body`元素添加一个8px的margin值，所以看起来我们的canvas边框像是一个frame，你可以通过css去除这个margin值。

```css
    body {
      margin: 0;
    }
```
这里我们先暂时保持原样。

此时，我们的canvas元素就可以通过DOM API来选择了，这里我们使用
`document.querySelector()`:

```js
    const canvas = document.querySelector('canvas')
```

## 修改canvas的背景颜色
在css中如下做：
```css
    canvas {
      background-color: lightblue;
    }
```

## 调整canvas大小
可以在css中设置高度、宽度
```css
    canvas {
      border: 1px solid black;
      width: 100%;
      height: 100%;
    }
```
以上代码使canvas充满整个外层元素。

如果canvas是html的第一个元素，将会充满整个body。

![](/images/JS_Weekly/the-html-canvas-guide/Screen Shot 2018-10-28 at 09.27.05.png)

如果想充满整个窗口，则可以：

```js
    canvas.width = window.innerWidth
    canvas.height = window.innerHeight
```

![](/images/JS_Weekly/the-html-canvas-guide/Screen Shot 2018-10-28 at 09.51.51.png)

如果在css中移除body的margin，并且设置背景颜色，则可以填充这个页面。

![](/images/JS_Weekly/the-html-canvas-guide/Screen Shot 2018-10-28 at 09.54.20.png)
如果窗口大小变化，我们也需要重新计算canvas的宽高。可以使用**debounce**函数来避免过于频繁的重新计算（当你用鼠标调整窗口大小时，`resize`事件会执行成百上千次）：
```js
    const debounce = (func) => {
      let timer
      return (event) => {
        if (timer) { clearTimeout(timer) }
        timer = setTimeout(func, 100, event)
      }
    }

    window.addEventListener('resize', debounce(() => {
      canvas.width = window.innerWidth
      canvas.height = window.innerHeight
    }))
```

## 获取canvas的上下文(context)
我们想在canvas上画画，首先需要获取上下文

```js
    const c = canvas.getContext('2d')
```

> 有时候把上下文变量设置为`c`,有时设置为`ctx`，都是“context”的简称

The [`getContext()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLCanvasElement/getContext)方法根据传的类型参数返回一个绘画上下文。

可用的值有：
*   `2d`, 本文我们用这个值
*   `webgl` WebGL版本1
*   `webgl2` WebGL版本2
*   `bitmaprenderer` 和[ImageBitmap](https://developer.mozilla.org/en-US/docs/Web/API/ImageBitmap)结合使用

根据上下文的类型，你可以给`getContext()`传递第二个参数来指定一些额外的选项。

在`2d`值下，我们有一个参数可以在所有浏览器中使用，`alpha`，默认值为true。如果设置为false，则浏览器认为canvas没有透明背景并且可以加快渲染速度。

## 开始绘画
有了上下文后，我们就可以开始绘制了。

我们有一些绘画方法可供选择，而且我们可以绘制：
*   text
*   lines
*   rectangles
*   paths
*   images
对于每种类型，我们都可以执行填充，描框，渐变，样式，阴影，旋转，缩放等大量操作。

让我们最简单的画一个矩形开始。

可以使用`fillRect(x, y, width, height)`方法来完成：

```js
    c.fillRect(100, 100, 100, 100)
```
上面代码会从(100,100)位置开始，画一个长宽都为100px的黑色矩形：

![](/images/JS_Weekly/the-html-canvas-guide/Screen Shot 2018-10-28 at 10.39.01.png)

你可以通过给`fillStyle()`传一些css的颜色字符串，来给矩形填充不同的颜色：
```js
    c.fillStyle = 'white'
    c.fillRect(100, 100, 100, 100)
```

通过这些，你可以开始创造性的绘画了：  

这种，
```js
    for (let i = 0; i < 60; i++) {
      for (let j = 0; j < 60; j++) {
        c.fillStyle = `rgb(${i * 5}, ${j * 5}, ${(i+j) * 50})`
        c.fillRect(j * 20, i * 20, 10, 10)
      }
    }
```

![](/images/JS_Weekly/the-html-canvas-guide/Screen Shot 2018-10-28 at 10.47.55.png)

或者这种：
```js
    for (let i = 0; i < 60; i++) {
      for (let j = 0; j < 60; j++) {
        c.fillStyle = `rgb(${i * 5}, ${j * 5}, ${(i+j) * 50})`
        c.fillRect(j * 20, i * 20, 20, 20)
      }
    }
```

![](/images/JS_Weekly/the-html-canvas-guide/Screen Shot 2018-10-28 at 10.48.21.png)

## 绘画元素
就像上文提到的，你可以画很多图案：
*   text
*   lines
*   rectangles
*   paths
*   images

我们只以矩形和文本为例执行对应的一些操作，您可以在[此处](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D)找到所需的所有其他API 。

### 修改颜色
可以通过`fillStyle`和`strokeStyle`属性来修改填充和边框的颜色，它们都接受css颜色，包括字符串和RGB等:
```js
    c.strokeStyle = `rgb(255, 255, 255)`
    c.fillStyle = `white`
```

### 矩形

*   clearRect(x, y, width, height)
*   fillRect(x, y, width, height)
*   strokeRect(x, y, width, height)

我们上一节中已经见过`fillRect()` 。`strokeRect()`的调用方式和它类似，但它不是填充矩形，而是使用当前笔触样式绘制笔划（样式可以使用`strokeStyle`属性更改）：
```js
    const c = canvas.getContext('2d')
    for (let i = 0; i < 61; i++) {
      for (let j = 0; j < 61; j++) {
        c.strokeStyle = `rgb(${i * 5}, ${j * 5}, ${(i+j) * 50})`
        c.strokeRect(j * 20, i * 20, 20, 20)
      }
    }
```

![](/images/JS_Weekly/the-html-canvas-guide/Screen Shot 2018-10-28 at 11.00.10.png)

`clearRect()`把一块区域设置为透明:

![](/images/JS_Weekly/the-html-canvas-guide/Screen Shot 2018-10-28 at 11.02.47.png)

### Text
绘制文字和矩形类似，有以下2个方法：

*   fillText(text, x, y)
*   strokeText(text, x, y)


`x`和`y`是相对于canvas画布左下角的值。

你可以使用canvas的`font`属性改变字体和大小:

```js
    c.font = '148px Courier New'
```

![](/images/JS_Weekly/the-html-canvas-guide/Screen Shot 2018-10-28 at 11.15.51.png)

下面是更改的与文本相关的其他属性（\* 是默认值）：

*   `textAlign` (start*, end, left, right, center)
*   `textBaseline` (top, hanging, middle, alphabetic*, ideographic, bottom)
*   `direction` (ltr, rtl, inherit*)

### Lines
要绘制一条线，首先调用`beginPath()`方法，然后用`moveTo(x, y)`提供一个起点，然后调用`lineTo(x, y)`来画线。最后调用`stroke()`：

```js
    c.beginPath()
    c.moveTo(10, 10)
    c.lineTo(300, 300)
    c.stroke()
```

线将根据`c.strokeStyle`得属性值进行着色。

## 一个复杂点的例子
这段代码生成了800个粒子：

![](/images/JS_Weekly/the-html-canvas-guide/Screen Shot 2018-10-28 at 14.38.54.png)
每个粒子都包含在canvas中，它的半径是随机的。

当你调整窗口大小时，元素会重绘。

你可以在[Codepen](https://codepen.io/flaviocopes/pen/qJvyVp)上运行代码。
```js
    const canvas = document.querySelector('canvas')
    canvas.width = window.innerWidth
    canvas.height = window.innerHeight
    const c = canvas.getContext('2d')

    const circlesCount = 800

    const colorArray = [
      '#046975',
      '#2EA1D4',
      '#3BCC2A',
      '#FFDF59',
      '#FF1D47'
    ]

    const debounce = (func) => {
      let timer
      return (event) => {
        if (timer) { clearTimeout(timer) }
        timer = setTimeout(func, 100, event)
      }
    }

    window.addEventListener('resize', debounce(() => {
      canvas.width = window.innerWidth
      canvas.height = window.innerHeight

      init()
    }))

    const init = () => {
      for (let i = 0; i < circlesCount; i++) {
        const radius = Math.random() * 20 + 1
        const x = Math.random() * (innerWidth - radius  * 2) + radius
        const y = Math.random() * (innerHeight - radius  * 2) + radius
        const dx = (Math.random() - 0.5) * 2
        const dy = (Math.random() - 0.5) * 2

        const circle = new Circle(x, y, dx, dy, radius)
        circle.draw()
      }
    }

    const Circle = function(x, y, dx, dy, radius) {
      this.x = x
      this.y = y
      this.dx = dx
      this.dy = dy
      this.radius = radius
      this.minRadius = radius
      this.color = colorArray[Math.floor(Math.random() * colorArray.length)]

      this.draw = function() {
        c.beginPath()
        c.arc(this.x, this.y, this.radius, 0, Math.PI * 2, false)
        c.strokeStyle = 'black'
        c.stroke()
        c.fillStyle = this.color
        c.fill()
      }
    }

    init()

```

## 另一个例子：在canvas上设置动画元素
基于上面的示例，我们通过循环函数为元素设置动画。  
每个圆圈都有自己的“生命”，并在画布的边界内移动。到达边界时，它会反弹：

我们使用`requestAnimationFrame()`，在迭代渲染每帧时稍微移动图像来实现这个效果。

## 与canvas上的元素交互
下面扩展上面的示例，使用户允许使用鼠标和粒子进行交互。

当您悬停在画布时，鼠标附近的粒子将会变大，当您移动到其他位置时，它们将恢复正常：

这要怎么实现呢？首先，我们使用2个变量跟踪鼠标的位置：

```js
    let mousex = undefined
    let mousey = undefined

    window.addEventListener('mousemove', (e) => {
      mousex = e.x
      mousey = e.y
    })
```
然后我们在Circle的update()方法中，使用这些变量来确定粒子的半径是否应该变大（或减小）：

```js
    if (mousex - this.x < distanceFromMouse && mousex - this.x > -distanceFromMouse && mousey - this.y < distanceFromMouse && mousey - this.y > -distanceFromMouse) {
      if (this.radius < maxRadius) this.radius += 1
    } else {
      if (this.radius > this.minRadius) this.radius -= 1
    }
```
`distanceFromMouse`是一个像素（设置为200）值，它定义了我们希望粒子对鼠标做出反应的范围。

## 性能

如果您尝试在上面的示例中添加更多粒子和移动元素，您可能会发现性能问题。  
浏览器会消耗大量资源来渲染画布的动画和交互，因此请注意你的代码可能在性能较差的机器上失去良好体验。

特别是当我尝试使用表情文字替代粒子时，我遇到了一些问题，我发现渲染文本需要更多的资源，因此界面会很快变得迟钝。

[MDN]((https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API/Tutorial/Optimizing_canvas)上给出了许多有关性能的提示，可以供参考。

## 结语
本文只是对canvas的简单介绍，利用它，你可以在页面上创造更多可能性，和构建令人惊叹的效果。

## 资源
* 原文：https://flaviocopes.com/canvas/
