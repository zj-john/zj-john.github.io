---
title: 使用D3和Vue创建交互式地图
categories:
  - notes
tags:
  - JavaScript Weekly
  - d3
  - vue
  - map
  - 2018.10.19
toc: true
date: 2018-10-26 17:51:56
---

![](/images/JS_Weekly/creating-an-interactive-map-with-d3-and-vue/jkbrhdhbrobnvcqoelwk.png)

## 为什么要这么做
把地图加到你的网站或者应用里有许多种方法，比如使用Google Maps, Mapbox, Leaflet等。  
而且用法很简单，有些服务商甚至只需您点击几下即可完成。

但是当你需要自定义设计，显示一些数据集或做你想做的其它事情时，它会变得越来越糟糕。而且，在Vue或React中你只能调用抽象好的javascript API，而不能使用JSX来渲染。

另外，好多库对私人项目也不是免费的。

所以，当我再一次要在地图上显示一些数据时，我决定：我要完全控制我的代码，然后用更好的方式创建我自己的地图。

<!-- more -->

## 第1步：创建静态地图
让我们用vue-cli 3，Babel和sass开始创建我们的应用程序开始

我们需要D3和[d3-tile](https://github.com/d3/d3-tile)（它不包含在d3的npm包中）来渲染地图块。

```shell
   yarn add d3 d3-tile
```
实际上我们不需要整个d3代码。对于一个简单的地图，我们只需要用d3-geo生成地图投影，用d3-tile生成切片。

接下来我们应该定义一些设置，如比例，宽度，高度和初始坐标。通常，我在mount时通过计算图表容器所在元素的大小动态确定图表大小。
```js
   <script>
   const d3 = {
     ...require('d3-geo'),
     ...require('d3-tile'),
   };

   export default {
     props: {
       center: {
         type: Array,
         default: () => [33.561041, -7.584838],
       },
       scale: {
         type: [Number, String],
         default: 1 << 20,
       },
     },
     data () {
       return {
         width: 0,
         height: 0,
       };
     },
     mounted () {
       const rect = this.$el.getBoundingClientRect();

       this.width = rect.width;
       this.height = rect.height;
     },
     render () {
       if (this.width <= 0 || this.height <= 0) {
         // the dummy for calculating element size
         return <div class="map" />;
       }

       return (
         <div class="map">our map will be here</div>
       );
     },
   };
   </script>

   <style lang="scss" scoped>
   .map {
     width: 100%;
     height: 100%;
   }
   </style>
```

现在定义投影生成器和切片生成器。
```js
   export default {
     // ...
     computed: {
       projection () {
         return d3.geoMercator()
           .scale(+this.scale / (2 * Math.PI))
           .translate([this.width / 2, this.height / 2])
           .center(this.center)
         ;
       },
       tiles () {
         return d3.tile()
           .size([this.width, this.height])
           .scale(+this.scale)
           .translate(this.projection([0, 0]))()
         ;
       },
     },
     // ...
   };
```
我总是把可计算的属性用到d3的函数中，因此当某些参数更改时，Vue会重新计算它们并更新我们的组件。

现在我们拥有了显示地图所需的一切，我们来渲染生成的图块：
```js
   export default {
     render () {
       if (this.width <= 0 || this.height <= 0) {
         return <div class="map" />;
       }

       return (
         <div class="map">
           <svg viewBox={`0 0 ${this.width} ${this.height}`}>
             <g>
               {this.tiles.map(t => (
                 <image
                   key={`${t.x}_${t.y}_${t.z}`}
                   class="map__tile"
                   xlinkHref={`https://a.tile.openstreetmap.org/${t.z}/${t.x}/${t.y}.png `}
                   x={(t.x + this.tiles.translate[0]) * this.tiles.scale}
                   y={(t.y + this.tiles.translate[1]) * this.tiles.scale}
                   width={this.tiles.scale}
                   height={this.tiles.scale}
                 />
               ))}
             </g>
           </svg>
         </div>
       );
     },
   };
```
在这里，我们由d3-tile生成切片,再从切片服务器请求图像。

您可以在[这里](https://wiki.openstreetmap.org/wiki/Tile_servers)找到其他服务商，或者甚至可以使用自定义样式托管[自己的服务](https://openmaptiles.org)。

不要忘记添加版权。
```html
   <div class="map__copyright">
     ©&nbsp;
     <a
       href="https://www.openstreetmap.org/copyright"
       target="_blank"
     >OpenStreetMap&nbsp;</a>
     contributors
   </div>
```

```css
   .map {
     // ...
     position: relative;
     font-family: Arial, sans, sans-serif;

     &__copyright {
       position: absolute;
       bottom: 8px;
       right: 8px;
       padding: 2px 4px;
       background-color: rgba(#ffffff, .6);
       font-size: 14px;
     }
   }
```
现在我们有了卡萨布兰卡的静态地图。

![Casablanca map](/images/JS_Weekly/creating-an-interactive-map-with-d3-and-vue/i5xqgdvy0qobkeyjffob.png)

## 第2步：添加地图控件
对我来说最令人兴奋的是Vue如何简化创建交互式地图的方式。我们只是更新了投影参数，地图就会同步更新。  
我们将来制作缩放按钮和拖动方式的位置控制。

让我们从拖动开始吧。  
我们需要在组件数据中定义投影转换属性，在svg元素上定义一些鼠标事件监听（或者你可以在tiles组上监听它们）。

```js
   <script>
   // ...

   export default {
     // ...
     data () {
       return {
         // ...
         translateX: 0,
         translateY: 0,

         touchStarted: false,
         touchLastX: 0,
         touchLastY: 0,
       };
     },
     computed: {
       projection () {
         return d3.geoMercator()
           .scale(+this.scale / (2 * Math.PI))
           .translate([this.translateX, this.translateY])
           .center(this.center)
         ;
       },
       // ...
     },
     mounted () {
       // ...
       this.translateX = this.width / 2;
       this.translateY = this.height / 2;
     },
     methods: {
       onTouchStart (e) {
         this.touchStarted = true;

         this.touchLastX = e.clientX;
         this.touchLastY = e.clientY;
       },
       onTouchEnd () {
         this.touchStarted = false;
       },
       onTouchMove (e) {
         if (this.touchStarted) {
           this.translateX = this.translateX + e.clientX - this.touchLastX;
           this.translateY = this.translateY + e.clientY - this.touchLastY;

           this.touchLastX = e.clientX;
           this.touchLastY = e.clientY;
         }
       },
     },
     render () {
       // ...
       return (
         <div class="map">
           <svg
             viewBox={`0 0 ${this.width} ${this.height}`}
             onMousedown={this.onTouchStart}
             onMousemove={this.onTouchMove}
             onMouseup={this.onTouchEnd}
             onMouseleave={this.onTouchEnd}
           >
             // ...
           </svg>
           // ...
         </div>
       );
     },
   };
   </script>

   <style lang="scss" scoped>
   .map {
     // ...

     &__tile {
       // reset pointer events on images to prevent image dragging in Firefox
       pointer-events: none;
     }
     // ...
   }
   </style>
```
哇！我们只是更新了translate的值，新图块就已经加载了。我们可以探索世界了。但是还没有变焦控制，让我们来实现它。

我们把`scale`加到组件的data中，并添加zoom属性和渲染缩放按钮。

根据我的经验，最小和最大平铺的缩放级别是10和27（老实说，我不太确定这对所有服务商都是正确的）。
```js
   <script>
   // ...

   const MIN_ZOOM = 10;
   const MAX_ZOOM = 27;

   export default {
     props: {
       center: {
         type: Array,
         default: () => [-7.584838, 33.561041],
       },
       initialZoom: {
         type: [Number, String],
         default: 20,
       },
     },
     data () {
       return {
         // ...
         zoom: +this.initialZoom,
         scale: 1 << +this.initialZoom,
       };
     },
     // ...
     watch: {
       zoom (zoom, prevZoom) {
         const k = zoom - prevZoom > 0 ? 2 : .5;

         this.scale = 1 << zoom;
         this.translateY = this.height / 2 - k * (this.height / 2 - this.translateY);
         this.translateX = this.width / 2 - k * (this.width / 2 - this.translateX);
       },
     },
     // ...
     methods: {
       // ...
       zoomIn () {
         this.zoom = Math.min(this.zoom + 1, MAX_ZOOM);
       },
       zoomOut () {
         this.zoom = Math.max(this.zoom - 1, MIN_ZOOM);
       },
     },
     render () {
       // ...
       return (
         <div class="map">
           <div class="map__controls">
             <button
               class="map__button"
               disabled={this.zoom >= MAX_ZOOM}
               onClick={this.zoomIn}
             >+</button>
             <button
               class="map__button"
               disabled={this.zoom <= MIN_ZOOM}
               onClick={this.zoomOut}
             >-</button>
           </div>
           //...
         </div>
       );
     },
   };
   </script>

   <style lang="scss" scoped>
   .map {
     // ...
     &__controls {
       position: absolute;
       left: 16px;
       top: 16px;
       display: flex;
       flex-direction: column;
       justify-content: space-between;
       height: 56px;
     }
     &__button {
       border: 0;
       padding: 0;
       width: 24px;
       height: 24px;
       line-height: 24px;
       border-radius: 50%;
       font-size: 18px;
       background-color: #ffffff;
       color: #343434;
       box-shadow: 0 1px 4px rgba(0, 0, 0, .4);

       &:hover,
       &:focus {
         background-color: #eeeeee;
       }

       &:disabled {
         background-color: rgba(#eeeeee, .4);
       }
     }
     // ...
   }
   </style>

```
只是两个步骤，我们就使用Vue，D3和OpenStreetMap创建了简单的交互式地图。

## 结论
使用D3和Vue来创建自己的地图组件并不困难。  
最重要的事情之一就是完全控制DOM，而不是使用一些抽象的地图渲染器API。

当然，要制作一个功能强大的地图，我们需要实现更多功能，如平滑缩放，最大边界等。  
不过所有的功能都是可定制的，所以你可以尝试你想做或需要的一切。


## 资源
* 原文： https://dev.to/denisinvader/creating-an-interactive-map-with-d3-and-vue-4158
* Source code: https://github.com/denisinvader/vue-d3-map
* Live preview: https://denisinvader.github.io/vue-d3-map/
