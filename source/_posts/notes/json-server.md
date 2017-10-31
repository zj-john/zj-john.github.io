---
title: json-server
categories:
  - notes
tags:
  - json-server
  - mock
date: 2017-09-15 11:27:32
toc: true
---

json-server是一款常用的mock插件。

>教程/源码
https://github.com/typicode/json-server


<!-- more -->

# 基本用法：
```Javascript
// 全局安装
npm install json-server -g
```

在package.json 文件中加入脚本
```Javascript
// 基本用法
// help 查看CLI说明
json-server -h

// cmd使用
json-server db.json --port 3003

// npm中使用
// --port 在3003端口启一个server 或简写为 -p
// server提供的api的内容是db.json中的数据
"scripts": {
  "mock": "json-server db.json --port 3003"
}
```
api中数据需要更复杂逻辑的话，文件内容可以用js文件替代，但输出必须是一个json格式的数据

```Javascript
// /mock/db.js
module.exports = function() {
  var data = { users: [] }
  // Create 1000 users
  for (var i = 0; i < 1000; i++) {
    data.users.push({ id: i, name: 'user' + i })
  }
  return data
}

// cmd启动
json-server db.js -p 3003
```

# 配置
可以在根目录（mock）下使用一个json文件对api进行配置：
```Javascript
// /mock/json-server.json
{
    "host": "0.0.0.0",
    "port": "3003",
    // 监听文件变化
    "watch": false,
    // response 延迟
    "delay": 500,
    // 不输入任何log
    "quiet": true,
    // 设置静态文件目录
    "static": "./public",
    // 进行转发过滤等特殊路由配置
    "routes": "./routes.json"
}

// /mock/routes.json
{
  // /news/1/show → /news/1
  "/news/:id/show": "/news/:id",
  // /api/posts # → /posts
  // /api/posts/1  # → /posts/1
  "/api/*": "/$1",
  // /posts/javascript # → /posts?category=javascript
  "/posts/:category": "/posts?category=:category",
  // /articles?id=1 # → /posts/1
  "/articles\\?id=:id": "/posts/:id"
}

// 运行
// 如果是json-server.json，config可省略
json-server db.js --config json-server.json
```
> 需要注意的是，路由必须以 / 开头

以下内容没有实际用处，知道就行。
> 返回静态文件：  
在根目录（如：mock）下设立public目录后，即可直接访问其下的所有静态文件，包括但不限于
js, css ,markdown 文件等,如：http://localhist:3003/readme.md。  
此时默认主页无法访问 :Returns default index file or serves ./public directory

# URL 过滤
```Javascript
// json内容示例
{
    "id": 0,
    "title": "11111",
    "desc": "111222333",
    "tag": "123",
    "views": 3810,
    "images": [
        "http://dummyimage.com/200x100/79f2a5&text=别角置",
        "http://dummyimage.com/200x100/f28279&text=收面几容受取",
        "http://dummyimage.com/200x100/7993f2&text=做件"
    ]
}


// 使用 . 操作对象属性值
// 获取图片数量为3，且标签字数为2的新闻
GET /news?images.length=3&tag.length=2

// 使用 _start 和 _end 或者 _limit 针对id
// 获取id=10开始的5篇新闻
GET /news?_start=10&_limit=5

// 获取id=20开始,id=35结束的新闻
GET /news?_start=20&_end=35

// 按照浏览数量降序排列
GET /news?_sort=views&_order=DESC

// 选取浏览量在2000-2500之间的新闻
GET /news?views_gte=2000&views_lte=2500

//使用 _ne 排除一个值
// 选择tag属性不是 "国际新闻" 的分类
GET /news?tag_ne=国际新闻

//使用 _like 进行模糊查找 (支持正则表达式)
// 查找title中含有 "前端" 字样的新闻
GET /news?title_like=前端

//使用 q，在对象全部value中遍历查找包含指定值的数据
// 查找新闻全部字段包含 "强拆" 字样的数据
GET /news?q=强拆
```

支持各种http方法，用法标准如下：
```
g.default routes
GET    /posts
GET    /posts/1
POST   /posts
PUT    /posts/1
PATCH  /posts/1
DELETE /posts/1
```

>中文mock推荐结合mock.js使用，支持更好
