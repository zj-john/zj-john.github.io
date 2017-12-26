---
title: 编写可维护的JavaScript-笔记
categories:
  - notes
tags:
  - 编写可维护的JavaScript
toc: true
date: 2017-12-11 12:36:42
---

![](/images/maintainable javascript.jpg)

<!-- more -->

# 编码规范

## 缩进
1. 空格 or Tab缩进，建议 4 空格为1个缩进。
2. 空格、Tab勿混用
3. 换行缩进：
```
// function 参数换行   结尾分号，前边8空格（2缩进）
callAFunction(document, element, window, "some input string", true, 123,
        navigator);
// 逻辑语句
if (isLeapYear && isFebruary && day === 29 &&
        live) {
    happyBirthDay();
}
// 变量赋值 对齐
let name = "z_j",
    email = "z_j@ctrip.com";
// object  1缩进
let book = {
    title: "HTML",
    author: "zs"
}
```

## 空行
* 方法间
* 赋值和方法间
* 注释前
* 方法内逻辑段落间

## 命名（驼峰式）
* 常量：大写 + 下划线 MAX_COUNT
* 变量：前缀名字 myName ：尽可能有意义，如果不好在字面体现含义，可加注释说明
* 函数：前缀动词 getName, isEnable
* 构造函数：大驼峰式 ApplicationList

## null
（对象的占位符）：返回 、判断

## undefined
避免人为使用undefined

## 注释
写给他人、写给自己

## if, for, while, do...while..., try...catch...finally
* 使用花括号
* 风格

```javascript
// google
if (isSuccess && data.length>0) {
}
// jquery
if ( isSuccess && data.length>0 ) {
}

for...in...:只用于遍历对象，且同时使用 hasOwnProperty()
let prop;

for (prop in object) {
    if (object.hasOwnProperty(prop)) {
        console.log("Property name is " + prop);
        console.log("Property value is " + object[prop]);
    }
}
```

## 声明
* 一个逻辑块中，变量声明提到顶部，并且合并
* 函数声明放在变量声明之后

## 函数
* 函数调用：doSomething(params) 无空格
* 立即执行函数用一对括号包裹起来（其本质相当于语句）

## 严格模式
“use strict“ 不要放在全局作用域，可以放在立即执行函数中（加一层作用域）

>检查点：
1. 句尾分号
2. 单行长度（仿java规范，小于80字符）
3. === or !==
4. “” or '' 统一，可以设立默认约定为“”双引号

# 实践规范
## 解耦
* 三元组解耦 - JS-HTML（事件绑定、JS外置、Template），CSS-HTML(CSS外置)
* 无耦合 NO Impossible

## 避免使用全局变量（作用域污染）
* 函数内部不使用外部变量，以参数传入
* 单全局变量：类库 JQuery $
* 零全局变量：立即执行函数
```javascript
(function(win) {
    let doc = win.document;
    // other code
}(window));
```

## 事件处理
* 隔离应用逻辑和用户行为
* 最小需要

```javascript
let MyApplication = {
    handleClick: function(event) {

        // 清除事件污染
        event.preventDefault();
        event.stopPropagation();
        // 应用逻辑
        this.showPopup(event.clientX, event.clientY);
    },
    showPopup: function(x, y) {
        // 内部逻辑
    }
};

addListener(element, "click", function(event) {
    MyApplication.handleClick(event);
});
```

## 值检测：
* typeof :原始值检测
* instanceof:引用值检测
* in: 属性检测
```javascript
if ("name" in object) {
    // 逻辑
}
// error
if (object["name"]) {
}
```

## 配置数据分离
* 常见配置数据： URL，重复的值，设置，展示给用户的值 （统一性）

```javascript
// 当前
export const status_render = function(data, type, full, row, meta) {
  if (!data) {
    return '-'
  }
  let statusType
  if (data === 'NORMAL') {
    statusType = `<span class="label webinfo-label-normal">正常</span>`
  } else if (data === 'FROZEN') {
    statusType = `<span class="label webinfo-label-warning">冻结</span>`
  }
  return statusType
}

// 改进
const STATUS_RENDER_CONFIG = {
    "NORMAL": {
        "text": "正常",
        "label": "label-normal",
        "icon": "fa-normal"
    }
};

export const status_render = function(data, type, full, row, meta) {
    let statusType = `<span class="label">-</span>`;
    if(data in STATUS_RENDER_CONFIG ) {
        statusType = `<span class="label ${ STATUS_RENDER_CONFIG[data.toUpperCase()]['label'] }">${ STATUS_RENDER_CONFIG[data.toUpperCase()]['text'] }</span>`
    }
    return statusType;
}
```
* 储存：JSON、JS


## 抛错
* throw new Error("message")
* try...catch

## 协作
* 不要随意更改（覆盖、新增、删除）非自己创建的对象，包含：原生对象（Object、Array等）、DOM、BOM、类库
* 如果确实需要，请继承

# 工具
1. [JSLint](http://www.jslint.com/) :最早，可定制型差，不建议使用
2. [JSHint](http://jshint.com/)：可配置参数定制，不支持自定义规则
3. [ESlint](https://eslint.org/)： 默认使用JSHint的规则，支持自定义。推荐
