---
title: JS将图片变为字符画
date: 2018-03-07 10:46:27
categories:
  - projects
tags:
  - js
  - canvas
  - image
  - code
toc: true
---
在刷头条的时候，看到这样一张图片，顺手存了下来。  
现在用JS来实现一下。  

- **功能**：将图片变为用字符组成的画
- **工具**：canvas
- **难度等级**: ★
- **项目地址**: https://github.com/zj-john/imageToCodeEffect

![](/images/codeEffect.jpg)
<!-- more -->

先上最终工具的[地址](https://zj-john.github.io/imageToCodeEffect/index.html),有兴趣的可以操作下。

# 编码之前

## 目标：  
上传一张图片，导出为图片对应的字符画。如果上传的图片过大，有可能导致生成的字符画过大，难以看到效果，所以还需要对上传的图片调整大小。

## 分析：  
1. 基本的想法是获取图片的每一个像素点，然后转换每个像素点，对应一个字符。
>canvas中处理的图片像素包括R,G,B,A四个点，也就是说每一个图片的像素点对应canvas解析结果中的四个值，分别是R（red值），G（green值），B（blue值），A（Alpha透明度值）

2. 拿上图来看，字符画是没有颜色概念的，我们实际处理的是一张灰度图像。所以需要转换rgb图像到灰度图像。
>常用的RGB转灰度的算法，[参考](https://www.cnblogs.com/zhangjiansheng/p/6925722.html)：
```js
// 方法1 心理学公式
Gray = R*0.299 + G*0.587 + B*0.114
// 方法2
Gray = (R*299 + G*587 + B*114 + 500) / 1000
Gray = (R*30 + G*59 + B*11 + 50) / 100
// 方法3 更快，调节各系数可以调节精度
Gray = (R*19595 + G*38469 + B*7472) >> 16
// 方法4 Adobe Photoshop中公式，效果好，慢
Gray = (R^2.2 * 0.2973 + G^2.2 * 0.6274 + B^2.2 * 0.0753)^(1/2.2)
// 方法5 精度最低 效果不好
GRAY = (R + G + B)/3
```


3. 灰度图像中每个像素点只包括RGBA中的A值，我们把A值（0~255）用我们要替代的字符划分区间后，落在区间中的像素值用对应的字符替换，就替换为了字符图。

4. 从上图中，我们看到很重要的部分在于留白，特别是背景的部分。所以对于灰度值最高的像素区间（灰度值最高为255，代表白色），我们用空白（空格）替换。

# 编码
## 上传图片
```js
uploadDom.addEventListener('change',handleFileSelect);

var reader = new FileReader();

function handleFileSelect(evt) {
    var file = evt.currentTarget.files[0];
    reader.onload = function (evt) {
        var src = evt.target.result;
				img.src = src;
        img.onload = function (argument) {
          //
          var data = imgDataToCode(this, newWidth.value);
  				// 写到<pre>标签中
  				result.innerHTML = data;
        }

    };
    reader.readAsDataURL(file);

};
```

## RGB图转灰度图
```js
// ascii_char是要使用的字符集
function rgbToGray(r, g, b) {
	var gray = parseInt((19595 * r + 38469 * g + 7472 * b) >> 16);
	var unit = 256.0/ascii_char.length ;
	return ascii_char[parseInt(gray/unit)];
}
```

## 把图片转为字符图
```js
function imgDataToCode(img, newWidth) {
  // 根据指定宽度，等比改变图片的大小
	const newHeight = parseInt(newWidth * img.height / img.width);
	canvas.width = newWidth;
	canvas.height = newHeight;
	ctx.drawImage(img, 0, 0, img.width, img.height, 0, 0, newWidth, newHeight);
  //读取整张图片的像素
	var imgData = ctx.getImageData(0, 0, newWidth, newHeight).data;

  // 像素值转换为灰度值
	var rgbList = [];
	var txt = "";
  var i=0;
	for (; i<imgData.length; i+=4) {
		//R - 红色（0-255）
		var red = imgData[i];
		// G - 绿色（0-255）
		var green = imgData[i+1];
		// B - 蓝色（0-255）
		var blue = imgData[i+2];
		// alpha 通道（0-255; 0 是透明的，255 是完全可见的）
		var alpha = imgData[i+3];
		rgbList.push({
			"r" : red,
			"g" : green,
			"b" : blue,
		});
	}

	var k = 0;
	for (; k< newHeight; k++) {
		var j = 0;
		for(; j < newWidth; j++) {
			var item = rgbList[k*newWidth + j];
			txt += rgbToGray(item.r, item.g, item.b);
		}
		txt += '\n';
	}
	return txt;
}
```

# 后续
1. 可填写用于填充的字符
2. 有一些色彩比较丰富的图片，增强对比度后再处理
3. UI
4. 彩色字符图案

# 参考文档
* 灰度转换：http://www.tannerhelland.com/3643/grayscale-image-algorithm-vb6/
