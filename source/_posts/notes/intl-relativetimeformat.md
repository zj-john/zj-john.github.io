---
title: The Intl.RelativeTimeFormat API
categories:
  - notes
tags:
  - JavaScript Weekly
  - javascript
  - Intl.RelativeTimeFormat
  - CLDR
  - 2018.10.26
toc: true
date: 2018-11-01 11:46:38
---

本文介绍一个2018标准的WEB API - **Intl.RelativeTimeFormat**，此API属于`Intl`对象,可以很方便和人性化的格式化时间的展示，特别是在要做国际化的站点上，尤其适用。
不过此API目前默认情况下只在V8 v7.1.179和Chrome 71中可用，不过你可以尝尝鲜。

现代Web应用程序通常使用“昨天”，“42秒前”或“3个月”之类的短语，而不是完整日期和时间戳。这种 _相对时间格式_ 已经变得如此普遍，以至于几个流行的库（包括[Moment.js](https://momentjs.com/)， [Globalize](https://github.com/globalizejs/globalize)和[date-fns](https://date-fns.org/docs/)都实现了按本地习惯格式化时间这样的功能。

实现本地化相对时间格式化的一个问题是，您需要为要支持的每种语言提供习惯词或短语列表（例如“昨天”或“上一季度”）。  
虽然[Unicode CLDR](http://cldr.unicode.org/)（通用语言环境数据仓库）提供此类数据，但您必须将其嵌入并与其他库一起才能使用它。遗憾的是，这不仅增加了包的大小，也对加载时间，解析/编译成本和内存消耗产生负面影响。

全新的`Intl.RelativeTimeFormat`API把这种负担转移到JavaScript引擎上，让JavaScript引擎来获取语言环境数据并供JavaScript开发人员直接使用。   
所以`Intl.RelativeTimeFormat`可以在不牺牲性能的情况下实现相对时间的本地格式化。

<!-- more -->

## 用法示例
下面展示的是在英语环境下相对时间的格式化。
```js
const rtf = new Intl.RelativeTimeFormat('en');  

rtf.format(3.14,  'second');  
// → 'in 3.14 seconds'  

rtf.format(-15,  'minute');  
// → '15 minutes ago'  

rtf.format(8,  'hour');  
// → 'in 8 hours'  

rtf.format(-2,  'day');  
// → '2 days ago'  

rtf.format(3,  'week');  
// → 'in 3 weeks'  

rtf.format(-5,  'month');  
// → '5 months ago'  

rtf.format(2,  'quarter');  
// → 'in 2 quarters'  

rtf.format(-42,  'year');  
// → '42 years ago'  
```
需要注意的是，传递给`Intl.RelativeTimeFormat`构造函数的参数可以是一个[标识BCP 47语言标签](https://tools.ietf.org/html/rfc5646)的字符串，也可以是一个次标注的[语言标签数组](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation)。


以下是使用简体中文的示例：
```js
const rtf =  new  Intl.RelativeTimeFormat('zh-cmn-Hans-CN');  

rtf.format(3.14,  'second');  
// → "3.14秒钟后"

rtf.format(-15,  'minute');  
// → "15分钟前"

rtf.format(8,  'hour');  
// → "8小时后"

rtf.format(-2,  'day');  
// → "2天前"  

rtf.format(3,  'week');  
// → "3周后"

rtf.format(-5,  'month');  
// → "5个月前"

rtf.format(2,  'quarter');  
// → "2个季度后"

rtf.format(-42,  'year');  
// → "42年前"
```
此外，`Intl.RelativeTimeFormat`构造函数还接受一个可选参数`options`，该参数可以对输出进行更细粒度的控制。  
为了说明灵活性，让我们根据默认设置英文查看更多输出：
```js
// 这里option中的值就是默认的配置，只不过这里显示的展示出来。
const rtf =  new  Intl.RelativeTimeFormat('en',  {  
  localeMatcher:  'best fit',  // other values: 'lookup'  
  style:  'long',  // other values: 'short' or 'narrow'  
  numeric:  'always',  // other values: 'auto'  
});  

// Now, let’s try some special cases!  

rtf.format(-1,  'day');  
// → '1 day ago'  

rtf.format(0,  'day');  
// → 'in 0 days'  

rtf.format(1,  'day');  
// → 'in 1 day'  

rtf.format(-1,  'week');  
// → '1 week ago'  

rtf.format(0,  'week');  
// → 'in 0 weeks'  

rtf.format(1,  'week');  
// → 'in 1 week'  
```
您可能已经注意到，上面的格式化中生成了字符串`'1 day ago'`而不是 `'yesterday'`，还有`'in 0 weeks'`而不是`'this week'`。发生这种情况是因为默认情况下，格式化程序使用数值作为默认输出。

要更改此行为，请将`numeric`选项设置为`'auto'`（而不是隐式默认值 `'always'`）：
```js
const rtf =  new  Intl.RelativeTimeFormat('en',  { numeric:  'auto'  });  

rtf.format(-1,  'day');  
// → 'yesterday'  

rtf.format(0,  'day');  
// → 'today'  

rtf.format(1,  'day');  
// → 'tomorrow'  

rtf.format(-1,  'week');  
// → 'last week'  

rtf.format(0,  'week');  
// → 'this week'  

rtf.format(1,  'week');  
// → 'next week'  
```
和`Intl`类下的其它方法类似，`Intl.RelativeTimeFormat`也有`format`和`formatToParts`方法。虽然`format`涵盖了大部分常见的用例，但如果您需要访问输出的各个部分，用`formatToParts`则会很有帮助：
```js
const rtf =  new  Intl.RelativeTimeFormat('en',  { numeric:  'auto'  });  

rtf.format(-1,  'day');  
// → 'yesterday'  

rtf.formatToParts(-1,  'day');  
// → [{ type: 'literal', value: 'yesterday' }]  

rtf.format(3,  'week');  
// → 'in 3 weeks'  

rtf.formatToParts(3,  'week');  
// → [{ type: 'literal', value: 'in ' },  
//    { type: 'integer', value: '3', unit: 'week' },  
//    { type: 'literal', value: ' weeks' }]  
```
有关其它选项及其行为的详细信息，请参阅提案库中的[API文档](https://github.com/tc39/proposal-intl-relative-time#api)。

## 最后
`Intl.RelativeTimeFormat`默认情况下在V8 v7.1.179和Chrome 71中可用。  
随着此API变得更加广泛可用，诸如[Moment.js](https://momentjs.com/)， [Globalize](https://github.com/globalizejs/globalize)和[date-fns](https://date-fns.org/docs/)之类的库，就可以使用更原生的相对时间格式化功能，这样即降低了对硬编码的[CLDR](https://en.wikipedia.org/wiki/Common_Locale_Data_Repository)数据库的依赖性，又可以提高加载时的性能，分析和编译时的性能，运行时的性能和内存的使用。

## 资源
* 原文 https://developers.google.com/web/updates/2018/10/intl-relativetimeformat
* Intl对象 https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl
* API文档 https://github.com/tc39/proposal-intl-relative-time#api
