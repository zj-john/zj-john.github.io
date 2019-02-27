---
title: 如何在Google地图上创建自定义HTML标记
categories:
  - notes
tags:
  - JavaScript Weekly
  - javascript
  - google maps
  - development
  - 2018.10.26
toc: true
date: 2018-10-30 10:52:02
---
![](/images/JS_Weekly/how-to-create-custom-html-markers-on-google-maps/1_DWTltmi3hvgfQawopMvnmw.jpeg)

基于Google Map的JavaScript API，在Google地图上创建标记的默认方法是使用图像或SVG路径，但这限制了开发的可能性。
本教程将介绍如何使用HTML和CSS在Google Map上创建地图标记。
该技术涉及Google Map的扩展库类`OverlayView`，为了适配它，我们还将介绍如何异步扩展新类以避免构建问题，竞争条件和重复依赖。

<!-- more -->

### 如何制作地图和标记
在现代应用程序中，大多数依赖项通过包管理器（如npm或yarn）添加，通过`import`语句引入使用，并通过构建工具（如webpack）构建打包到高性能代码块中。
但对Google地图来说，通常通过脚本标记（\<script\>）加载到web应用的HTML文件中，然后将Google Maps的JavaScript库放在全局对象（window.google）上以供运行时使用。

Google Maps 的JS API中，每个功能都在`google.maps`这个父命名空间下拥有自己的类/命名空间。  
比如，向地图添加标记的最简单方法是使用`google.maps.Marker`下的[Marker类](https://developers.google.com/maps/documentation/javascript/reference/marker#Marker)。这个接口用于在地图上创建标记，它仅接受两种类型的标记内容实体：图像或SVG。
这虽然适用于很多情况，但仍然限制了开发人员的操作需求。

Google Maps的JS API还有一个用于在地图上创建实体的更通用的类：[OverlayView类](https://developers.google.com/maps/documentation/javascript/reference/overlay-view#OverlayView)，位于`google.maps.OverlayView`下。
文档中称这些为“自定义叠加层(Custom Overlays)”，并将它们定义为“地图上与纬度/经度坐标相关联的对象，因此当您拖动或缩放地图时它们会跟随移动。”

这个描述的效果和使用Marker类定义的地图标记很类似！事实上，`google.maps.Marker`只是一种预定义的叠加类型。因此，通过使用`OverlayView`类，开发人员可以使用任何HTML内容来创建满足需求的地图标记。由于自定义叠加的内容只是HTML，因此CSS和JavaScript都可以通过元素的id或类属性来定位自定义叠加元素，从而达到更精细地控制行为，更灵活的内容和CSS动画！

### 创建HTMLMapMarker类
我们将继承`OverlayView`类，创建一个名为`HTMLMapMarker`的ES6的类。构造函数将采用三个参数：

1. 纬度/经度对象。这是另一个Google Maps类：[LatLng类](https://developers.google.com/maps/documentation/javascript/reference/coordinates) （`google.maps.LatLng`）的实例。此类的构造函数有两个`Number`参数，分别表示标记在地图上应放置位置的纬度和经度。
2. 表示标记内容的HTML。以字符串格式表示。对于使用webpack构建的应用程序，可以是通过[html-loader](https://github.com/webpack-contrib/html-loader)导入的HTML文件。
3. 接受标记的Google Map实例。

我们的类还需要重写`OverlayView`类的以下四种方法：
Our class will also need to implement four methods to satisfy the requirements of an `OverlayView`:

1.  `draw()`. 此方法将用于在地图上绘制标记。
2.  `remove()`. 此方法将用于从地图中删除标记。
3.  `getPosition()`. 此方法将用于跟踪标记放置的位置（纬度/经度）。
4.  `getDraggable()`. 此方法将指示我们的标记是否可拖动。既然不能，就返回 `false`。

让我们从类定义和构造函数开始。我们将通过具有上述三个属性的对象`args`将参数传递给构造函数：

```js
class HTMLMapMarker extends OverlayView {
  constructor() {
    super();
    this.latlng = args.latlng;
    this.html = args.html;
    this.setMap(args.map);
  }

  // Remainder of class definition  
}
```

如上所示，传递给构造函数的LatLng和HTML会存为同名（`this.latlng`和`this.html`）的实例属性。而`map`实例会立即使用。在构造函数中，我们调用从`OverlayView`继承的方法`setMap`，传入`map`实例，将新标记链接到页面上的当前`map`实例。

接下来，让我们继续讨论该`draw`方法。这个方法完成了大部分繁重的工作。


```js
draw() {
  if (!this.div) {
    this.div = document.createElement('div');
    this.div.style.position = 'absolute';
    if (this.html) {
      this.div.innerHTML = this.html;
    }
    google.maps.event.addDomListener(this.div, 'click', event => {
      google.maps.event.trigger(this, 'click');
    });
    const panes = this.getPanes();
    panes.overlayImage.appendChild(this.div);    
  }
  const point = this.getProjection().fromLatLngToDivPixel(this.latlng);
  if (point) {
    this.div.style.left = `${point.x}px`;
    this.div.style.top = `${point.y}px`;
  }
}
```

第一步是检查是否`this.div`已定义。`<div>`中将保存最终放置在地图上的标记元素。如果没有定义，我们将通过`document.createElement(“div”)`创建一个新的`<div>`，并将其存储`this.div`，并将其`position`设置为`absolute`。

然后，把`<div>`的`innerHTML`属性设置为构造函数中已赋值的`this.html`，这完成了标记元素的HTML内容。

在方法的第8行，我们使用`google.maps.event`类的方法`addDomLister`向我们的新div添加一个事件监听器。该方法以`this.div`为第一个参数，事件名称（'click'）作为第二个参数，以及在事件发生时执行的回调作为第三个参数。在我们的例子中，回调参数只需要将click事件传递给`this`，即使用中已实例化的类，稍后会详细介绍。

该`draw`方法的第10行使用从`OverlayView`中继承的方法`getPanes`来获取当前地图实例的窗格。窗格是Google对图层的叫法，每个图层上都可以放置用于在地图上显示的各种实体。有关窗格的更多信息，您可以在此处阅读[文档](https://developers.google.com/maps/documentation/javascript/reference/overlay-view#MapPanes)。我们使用`overlayImage`窗格的`appendChild`方法来追加我们存储在`this.div`中的元素`<div>`。

`draw`方法的最后一步是将我们新放置的`<div>`定位到适当的纬度/经度位置。这可以通过使用继承方法`getProjection`获取地图投影，然后在返回的投影上调用`fromLatLngToDivPixel`方法来完成。此方法将提供的纬度/经度转换为屏幕上对应的x，y位置。然后，我们将div的`left` 和`top`属性分别设置为x和y值。这也是基于像素的偏移量，添加渲染元素的理想位置，在本文最后的演示中，您会注意到我已经在高度和宽度上添加了25px的偏移，因为图像是50px的长宽，最后得到的效果正好使图像在所提供的纬度/经度上居中。

我们类中的`draw`方法就完成了，但一点点重构可以提高可读性。我选择了将功能的逻辑重构到三种辅助方法：`createDiv`, `appendDivToOverlay`和`positionDiv`。如下：

```js
createDiv() {
  this.div = document.createElement('div');
  this.div.style.position = 'absolute';
  if (this.html) {
    this.div.innerHTML = this.html;
  }
  google.maps.event.addDomListener(this.div, 'click', event => {
    google.maps.event.trigger(this, 'click');
  });
}

appendDivToOverlay() {
  const panes = this.getPanes();
  panes.overlayImage.appendChild(this.div);
}

positionDiv() {
  const point = this.getProjection().fromLatLngToDivPixel(this.latlng);
  if (point) {
    this.div.style.left = `${point.x}px`;
    this.div.style.top = `${point.y}px`;
  }
}

draw() {
  if (!this.div) {
    this.createDiv();
    this.appendDivToOverlay();
  }
  this.positionDiv();
}
```
接下来看`remove`方法：

```js
remove() {
  if (this.div) {
    this.div.parentNode.removeChild(this.div);
    this.div = null;
  }
}
```

此方法只检查`this.div`中HTML元素是否存在。如果它存在，我们可以通过在它的`parentNode`上调用`removeChild` 来从DOM中删除它。一旦从DOM删除，我们也设置`this.div`为`null`，使实例的引用也被删除。

剩下的方法，`getPosition`和`getDraggable`都非常简单：

```js
getPosition() {
  return this.latlng;
}

getDraggable() {
  return false;
}
```

`getPosition`方法只返回保存在`this.latlng`的纬度/经度，`getDraggable`则返回布尔值`false`，因为我们的标记不可拖动。

我们已经完成了类的定义。最终的代码应如下所示：

```js
class HTMLMapMarker extends OverlayView {
  constructor() {
    super();
    this.latlng = args.latlng;
    this.html = args.html;
    this.setMap(args.map);
  }

  createDiv() {
    this.div = document.createElement('div');
    this.div.style.position = 'absolute';
    if (this.html) {
      this.div.innerHTML = this.html;
    }
    google.maps.event.addDomListener(this.div, 'click', event => {
      google.maps.event.trigger(this, 'click');
    });
  }

  appendDivToOverlay() {
    const panes = this.getPanes();
    panes.overlayLayer.appendChild(this.div);
  }

  positionDiv() {
    const point = this.getProjection().fromLatLngToDivPixel(this.latlng);
    if (point) {
      this.div.style.left = `${point.x}px`;
      this.div.style.top = `${point.y}px`;
    }
  }

  draw() {
    if (!this.div) {
      this.createDiv();
      this.appendDivToOverlay();
    }
    this.positionDiv();
  }

  remove() {
    if (this.div) {
      this.div.parentNode.removeChild(this.div);
      this.div = null;
    }
  }

  getPosition() {
    return this.latlng;
  }

  getDraggable() {
    return false;
  }
}
```

### 在运行时定义OverlayView
由于我们一直基于Google Maps JavaScript库提供的类`OverlayView`进行开发，因此我们需要保证在使用时，`google.maps.OverlayView`已经定义。

如果您的应用不需要构建过程，且在定义类时全局`google.maps`对象已经定义，则可以直接继承`OverlayView`：

```js
class HTMLMapMarker extends google.maps.OverlayView {}
```

但是，如果您正在一个需要构建过程的项目中工作，那么在您尝试让`HTMLMapMarker`继承`google.maps.OverlayView`类时，它可能还没有定义。虽然直接将`OverlayView`类添加到项目中可能会解决问题，但它会导致代码重复，因为在加载Google Maps库时`OverlayView`将始终加载。

由于闭包的强大功能，我们可以包装我们的类`google.maps.OverlayView`以保证在使用时完成定义。没有竞争条件，没有重复的代码。

![](/images/JS_Weekly/how-to-create-custom-html-markers-on-google-maps/1_qAtO25mhi5dKlOzc8m9a-g.jpeg)

让我们来命名我们的闭包函数为`createHTMLMapMarker`。此函数接受一个具有两个属性的对象：一个具有默认值的`google.maps.OverlayView`的属性`OverlayView`，以及一个[rest运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters)（…args）来收集传递给`createHTMLMapMarker`的参数。

```js
const createHTMLMapMarker = ({ OverlayView = google.maps.OverlayView,  ...args }) => {
  class HTMLMapMarker extends OverlayView {
    // Remainder of class definition
  }

  return new HTMLMapMarker();
};

export default createHTMLMapMarker;
```

通过这个小的改变，我们可以保证在我们调用`createHTMLMapMarker`时`google.maps.OverlayView`已经定义。

现在我们已经完成了我们的类和闭包，让我们继续学习！
本教程的其余部分将采用以下文件目录：

```
/index.html
/src/index.js
/src/html-map-marker.js <-- Our closure + class definition
/src/style.css
```

出于我们的教程目的，`index.html`需要只需加载我们的CSS，加载Google Maps JavaScript库，然后加载应用程序的入口点`src/index.js`。页面上唯一的元素是ID为`map`的`<div>`，它用于显示Google Map。

```html
<html>
  <head>
    <title>Custom HTML Map Markers</title>
    <meta charset="UTF-8" />
    <link rel="stylesheet" href="src/style.css">
  </head>
  <body>
    <div id="map"></div>
    <script src="https://maps.googleapis.com/maps/api/js?v=3.exp"></script>
    <script src="src/index.js"></script>
  </body>
</html>
```

`index.js`将定义一个以Timbuktu的纬度和经度为中心的，新Google Maps 实例`map`。然后，它将使用我们之前定义的闭包函数`createHTMLMapMarker`定义一个`marker`，以相同的`latlng`、`map`实例，和将呈现为标记内容的`html`作为参数传入。我们将使用派对鹦鹉作为图像标记，定义ID属性为`parrot`以便后续增加样式。

```js
import createHTMLMapMarker from "./html-map-marker.js";

const latLng = new google.maps.LatLng(16.7666, -3.0026);
const mapOptions = {
  zoom: 11,
  center: latLng
};
const map = new google.maps.Map(document.getElementById("map"), mapOptions);

let marker = createHTMLMapMarker({
  latlng: latLng,
  map: map,
  html: `<img id="parrot" src="https://cultofthepartyparrot.com/parrots/hd/parrot.gif">`
});

marker.addListener("click", () => {
  alert("Partyin Partyin Yeah!");
});
```

还记得我们在类`HTMLMapMarker`的`draw`方法中定义了“click”的DOM事件吗？如前所述，click事件只是传递给`marker`实例。这允许我们通过`addListener`方法直接向我们新创建的`marker`添加一个事件监听器，如第16-18行所示`index.js`。

下面是设置的样式：

```css
html, body {
  height: 100%;
  margin: 0;
  padding: 0;
}
#map {
  height: 100%;
}
#parrot {
  border: 1px solid #f00;
  border-radius: 20%;
  height: 50px;
  width: 50px;
}
```

完成！通过我们的新的`createHTMLMapMarker`功能，我们现在可以轻松地将任何HTML添加到Google地图实例中。您可以在下面看到教程的最终结果。

![](/images/JS_Weekly/how-to-create-custom-html-markers-on-google-maps/demo.png)

_请注意，下面的代码示例（CodeSandbox中）还会收到来自Google的提醒“此页面无法正确加载Google地图”，因为没有使用任何API密钥。不过该应用程序仍应按预期工作。_

## 资源
* 原文: https://levelup.gitconnected.com/how-to-create-custom-html-markers-on-google-maps-9ff21be90e4b
* Demo: https://4q0w9wq91w.codesandbox.io/
