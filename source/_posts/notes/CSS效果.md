---
title: CSS效果
categories:
  - notes
tags:
  - css
toc: true
date: 2018-06-12 11:25:42
---

## 虚线
### 效果
<div style="height: 1px;background: linear-gradient(to right, #ff8080, #ff8080 7.5px, transparent 7.5px, transparent);background-size: 10px 100%;
"></div>



### 代码
```html
<div style="
  height: 1px;
  background: linear-gradient(to right, #ff8080, #ff8080 7.5px, transparent 7.5px, transparent);
  background-size: 10px 100%;
"></div>
```
### 分析

一般想到虚线，可能会考虑<b>hr</b>这个标签,或者是用border的样式，这种方式比较便捷，但是缺点在于无法定制。  
所以这里介绍的虚线用到的是 <b>linear-gradient</b> 这个css属性，可以定制化虚线的样式。

语法：
```js
<linear-gradient> = linear-gradient([ [ <angle> | to <side-or-corner> ] ,]? <color-stop>[, <color-stop>]+)
<side-or-corner> = [left | right] || [top | bottom]
<color-stop> = <color> [ <length> | <percentage> ]?
```
角度：
* angle：用角度值指定渐变的方向（或角度）。
* to left：设置渐变为从右到左。相当于: 270deg
* to right: 设置渐变从左到右。相当于: 90deg
* to top：设置渐变从下到上。相当于: 0deg
* to bottom：设置渐变从上到下。相当于: 180deg。这是默认值，等同于留空不写。

color-stop: 用于指定渐变的起止颜色：
* color：指定颜色。
* length：用长度值指定起止色位置。不允许负值
* percentage：用百分比指定起止色位置。

我们来看background: linear-gradient(to right, #ff8080, #ff8080 7.5px, transparent 7.5px, transparent)这句代码：
1. to right，表示向右渐变，
2. #ff8080, #ff8080 7.5px, 这两个点表示从0画一个7.5px的#ff8080颜色的实线（因为颜色没有改变）
3. #ff8080 7.5px, transparent 7.5px，这一步什么都没有做，因为他们的位置都是7.5px，也就是渐变的长度为0
4. transparent 7.5px, transparent，是一段透明的距离，从7.5px到10px(background size是10px)，共2.5px  

所以以上就画出一个实线(7.5px):透明(2.5px) = 3:1的虚线


## 箭头
### 效果
<div style="width:10px;height:10px;border:10px solid #FFF;border-left-color:red;"></div>

### 代码

```html
<div style="
  width:10px;
  height:10px;
  border:10px solid #FFF;
  border-left-color:red;
"></div>
```

### 分析
先说盒装模型，每一侧的border是个等边梯形（非长方形）。所以如果border和宽高相同，也就是说整个模型只剩下border（4个三角形）。  
此时把某三边的border隐藏掉，剩下的模型看起来就是个三角形了，也就是箭头


## 圆圈
### 效果
<div style="height: 20px;width: 20px;border-radius:10px;border:1px solid;"></div>


### 代码
```html
<div style="height: 20px;
  width: 20px;
  border-radius:10px;
  border:1px solid;">
</div>
```

### 分析
圆圈的分析比较简单，div的width和height相同，border的radius是width的一半就行。想像一下正方形的内切圆。

## 参考
* 虚线Sample：http://www.zhangxinxu.com/study/201710/css3-linear-gradient-dashed-generate.html
* linear-gradient： https://www.jianshu.com/p/bf862535dd30
* http://www.css88.com/book/css/values/image/linear-gradient().htm
