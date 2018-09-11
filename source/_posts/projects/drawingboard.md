---
title: 绘画板
date: 2018-05-24 17:13:53
categories:
  - projects
tags:
  - js
  - canvas
  - fabric
  - ant-mobile
toc: true
---

最近看canvas，拿这个项目作为一个验收结果。  

- **功能**：选择主题（漫画、自定义），选择模块，在画板上布局编辑，最后保存为一张图片
- **工具**：canvas, fabric, ant-mobile
- **难度等级**: ★★
- **项目地址**: https://github.com/zj-john/drawingBoard

<!-- more -->

先上最终工具的[地址](https://zj-john.github.io/drawingBoard/),有兴趣的可以操作下。

>由于是mobile框架，可以在浏览器手机端模式下浏览或在手机浏览器打开操作

# fabric
## 特性
[fabric](http://fabricjs.com/)是个强大而简单的H5 canvas库，对canvas的基础功能做了很多封装，并提供一些优化的操作，包括不限于：
* 模块：包括矩形、文字、Path、Image、Group（组合）、IText（可编辑）、Free Drawing等多种模块
* 属性：每个模块可以独立设置Event，可以独立设置是否可以拖动、自定义样式等
* 动画
* 图片Filter
* svg处理

## 使用
node下使用：[文档](http://fabricjs.com/fabric-intro-part-4#node)

我demo中用的是fabric-webpack
```js
import {fabric} from 'fabric-webpack';

let canvas = new fabric.Canvas(domId, {
  backgroundColor: "white"
});
```

## 问题
IText中PC Chrome浏览器中无法输入中文，手机端测试可以，issue上看和键盘编码有关


# canvas保存图片
## a标签
```js
let a = document.createElement('a');
a.href = canvas.toDataURL('image/png');
a.download = "download.png";
a.click();
```
## FileSaver
```js
const FileSaver = require('file-saver');

canvas.getElement().toBlob(function(blob) {
    FileSaver.saveAs(blob, "download.png");
});
```

## 问题
PC浏览器中，可以保存下载，手机端不行。  
于是采用了[文档](https://juejin.im/post/5a17c5e26fb9a04527254689)中的方法，生成图片，不直接下载。

# 可替换图片的Img组件

## 代码
```js
import defaultPic from './default_pic.png';

const global = this;
fabric.Image.fromURL(defaultPic, function(oImg) {
  let canvas = global.canvas,
      height = canvas.height,
      width = canvas.width;
  const options = {
    'left': 0.5*width-50,
    'top': 0.5*height-50,
    'width':100,
    'height':100
  }
  oImg.set(options);
  // 长按
  oImg.on('mouseup', function() {
    let fileInput = document.createElement("input");
    fileInput.type = "file",
    fileInput.accept ="image/gif,image/jpeg,image/jpg,image/png";
    fileInput.addEventListener("change", function(event) {
      let file = event.currentTarget.files[0],
          reader = new FileReader();
      reader.onload = function (evt) {
        let img = document.createElement("img");
        img.src = evt.target.result;
        img.onload = function() {
          oImg.setElement(img);
          oImg.set(options);
          canvas.renderAll();
        }
      };
      reader.readAsDataURL(file);
    }, false)
    fileInput.click();
  })
  canvas.add(oImg);
});
```
## 问题
image上longpress、mousedblclick等事件未生效

# 参考文档
1. https://juejin.im/post/5a17c5e26fb9a04527254689
