---
title: 在JavaScript中搜索和排序含有变音符号的文本
categories:
  - notes
tags:
  - JavaScript Weekly
  - 变音符号
  - javascript
  - localeCompare
  - normalize
  - 2018.10.19
toc: true
date: 2018-10-23 11:33:00
---

最近我们忙着使用[React](https://reactjs.org)重构之前的一个项目的前端代码，其中包括重构我们的浏览筛选功能，如下：

 ![Searchable browse filters on Thread](/images/JS_Weekly/searching-and-sorting-text-with-diacritical-marks-in-javascript/browse-b4c0e009eab497d85647dcd2d556641e-1c09f.png)

客户可以使用这些过滤器，品牌，颜色或价格等，来帮助他们更轻松地找到自己想要的产品。

因为我们的某些过滤器包含很多可选选项，所以需要让它们易于搜索和排序十分重要。

如果您希望用户可以从可能包含变音符号的数据列表中搜索结果，本文展示了一项很棒的技巧。
<!-- more -->

### 排序

以下列品牌为例：
```js
const brands = [
 { label: "Côte et Ciel", value: 1532 },
 { label: "Études", value: 17 },
 { label: "AllSaints", value: 2501 },
 { label: "Samsøe & Samsøe", value: 1571 },
 { label: "Ben Sherman", value: 2124 },
 { label: "Drôle de Monsieur", value: 137 },
 { label: "!Solid", value: 668 },
];
```

怎么做才能直观地对它们进行排序呢？

最初在JavaScript中对它们进行排序似乎很简单：只需对brands数组调用`sort()`，然后传递一个 比较两个标签的函数 进去即可，例如：
```js
brands.sort((a, b) => {
  return a.label > b.label;
});
/* 结果：
[
  { label: "!Solid", value: 668 },
  { label: "AllSaints", value: 2501 },
  { label: "Ben Sherman", value: 2124 },
  { label: "Côte et Ciel", value: 1532 },
  { label: "Drôle de Monsieur", value: 137 },
  { label: "Samsøe & Samsøe", value: 1571 },
  { label: "Études", value: 17 },
]
*/
```

除了`Études`错误地放置在列表底部之外，其它排序顺序是对的。  
JavaScript中是按字母顺序比较字符串的，不过这么说不太准确。  
实际上，它是用字符在Unicode表中的位置来确定其排序的。

如果我们查看字符[z](https://unicode-table.com/en/007A/)和[é](https://unicode-table.com/en/00E9/)，我们可以看到以下内容：

| Character | Encoding | hex | dec (bytes) | dec | binary |
| ------------- | ------ | ----- | ----|----|----|----|
|z|UTF-8|7A|122|122|01111010|
|é|UTF-8|C3 A9|195 169|50089|11000011 10101001|

注意字符的十进制值; `é`具有更高的值，所以它的顺序会排在`z`之后。

幸运的是，JavaScript有`String.prototype.localeCompare`函数：我们可以将上述函数简单重写为：
```js
brands.sort((a, b) => {
  return a.label.localeCompare(b.label);
});
/*
[
  { label: "!Solid", value: 668 },
  { label: "AllSaints", value: 2501 },
  { label: "Ben Sherman", value: 2124 },
  { label: "Côte et Ciel", value: 1532 },
  { label: "Drôle de Monsieur", value: 137 },
  { label: "Études", value: 17 },
  { label: "Samsøe & Samsøe", value: 1571 },
]
*/
```

了解localeCompare的其他参数请阅读[文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/localeCompare)。

### 搜索
排序很容易解决，但是搜索呢？

我们提供了一个搜索input，可以方便的匹配您的输入，返回满足的列表，但搜索`Cote`不会产生结果`Côte et Ciel`。
```js
const brands = [
  { label: "Côte et Ciel", value: 1532 },
  { label: "Études", value: 17 },
  { label: "AllSaints", value: 2501 },
  { label: "Samsøe & Samsøe", value: 1571 },
  { label: "Ben Sherman", value: 2124 },
  { label: "Drôle de Monsieur", value: 137 },
  { label: "!Solid", value: 668 },
];

brands.filter(brand => {
  return brand.label.indexOf("Cote") > -1;
})

/*
=> []
*/

brands.filter(brand => {
  return brand.label.indexOf("Côte") > -1;
})

/*
[
  { label: "Côte et Ciel", value: 1532 },
]
*/
```

我们不能期望客户知道我们使用了变音符号的品牌拼写，也不能期望客户能够轻松输入正确的字符，因为这是不合理的。

因此，为了让客户能够找到这些品牌，我们需要从字符中删除变音符号，然后再将其与搜索查询进行比较。

我们可以分两部分来完成。

首先，我们需要“分解”字符串，以便任何带有变音符号的字符由其双字节对表示。这是UTF-8中使用的一种技术，将单个字符表示为一个双字节对。

我们可以使用JavaScript中的[`normalize`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/normalize)函数执行此操作，并使用“[NFD](Normalization Form Canonical Decomposition)”格式。

通过文档，我们可以看到“NFD”代表“规范化形式规范分解”，重点是“分解”。

我们可以通过比较分解前后的字符串长度来看到效果：
```js
"Côte et Ciel".length
/*
12
*/

"Côte et Ciel".normalize('NFD').length
/*
13
*/
```

这表明该`ô`字符现在由两个字节而不是一个字节表示。

然后，现在我们为字符和变音符号都规范化了字节，我们可以使用以下`String.prototype.replace`函数删除不需要的字符：
```js
const brands = [
  { label: "Côte et Ciel", value: 1532 },
  { label: "Études", value: 17 },
  { label: "AllSaints", value: 2501 },
  { label: "Samsøe & Samsøe", value: 1571 },
  { label: "Ben Sherman", value: 2124 },
  { label: "Drôle de Monsieur", value: 137 },
  { label: "!Solid", value: 668 },
];

brands.filter(brand => {
  const decomposedLabel = brand.label.normalize('NFD').replace(/[\u0300-\u036f]/g, '');
  return decomposedLabel.indexOf("Cote") > -1;
})
/*
[
  { label: "Côte et Ciel", value: 1532 },
]
*/
```

`replace` 函数中包含一系列Unicode点，从[`U+0300`](https://unicode-table.com/en/#0300) 到 [`U+036F`](https://unicode-table.com/en/#036F)，涵盖我们可能遇到的音调符号字节。

## 资源
* 原文 https://thread.engineering/2018-08-29-searching-and-sorting-text-with-diacritical-marks-in-javascript/
* normalize: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/normalize
* localeCompare: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/localeCompare
