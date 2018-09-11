---
title: 爬虫爬取房价数据
date: 2018-03-06 11:31:55
categories:
  - projects
tags:
  - crawler
  - house
toc: true
---

今年考虑买房，想着做点什么，所以爬取了Fang.com的数据……
>但爬取的数据在买房中基本用不上，如果想着依托于各种数据来作分析，那真的需要你有扎实的数据分析基础和对房价市场的了解。不然面对一大堆数据可能也不知从何入手还有可能造成误区。

- **功能**：爬取房价、房型等数据
- **工具**：node, crawler
- **难度等级**: ★
- **项目地址**: https://github.com/zj-john/houseCrawler

<!-- more -->

# 编码之前
## 目标
爬取网站所有二手房的数据信息，信息采集需要精确到具体房子的detail页面中，爬取的数据放在本地的数据库中

## 依赖的包
```js
// 连接数据库
const mysql = require('mysql');
// 爬虫
const crawler = require("crawler");
// 逻辑处理时使用
const url = require('url');
const moment = require('moment');
const _ = require('lodash');
const path = require('path');
// 给每次的request请求添加user-agent，来伪装成浏览器访问
// https://github.com/sindresorhus/ua-string Get the user agent of a recent Chrome version to pretend to be a browser in network requests
const uaString = require('ua-string');
```

# 编码
## 爬取
```js
// 实例化一个爬虫
const c = new crawler({
    maxConnections: 1,
    rateLimit: 5000, // gap 5 sec
    timeout: 45000,
    userAgent: uaString
});

// 开始爬取，使用listFetcher处理爬取的结果
c.queue([
    {
        uri: 'http://esf.sh.fang.com/',
        callback: listFetcher,
        city: '上海'
    }
]);

const listFetcher = (error, res, done) => {
    if(error){
      // 错误处理
      let export_error = "URL: " +  res.options.uri + ";Error: " + error;
      utils.export_to_file(export_error, "./error/" + moment().format("YYYY-MM-DDTHH-mm-ss") + '.txt');
    }else{
        let $ = res.$
        // 处理程序
        // 使用$像jQuery一样处理dom，获取你需要的数据。
    }
    done();
}


// Emitted when queue is empty.
c.on('drain',function(){
    console.log('done!');
});

// Emitted when crawler is ready to send a request.
c.on('request',function(options){
    let now = Date();
    console.log (`${now}[request][${options.city}]${options.uri}`);
});
```

## SQL
```js
db.connect();
// 对DB的操作就是执行sql语句
db.query('INSERT INTO fangtianxia_old SET ?', data, function (err, rows, fields) {
 if(err){
    console.log('INSERT ERROR - ', err.message);
    return;
   }
   // console.log("INSERT SUCCESS");
});
db.end()

```

# 后续
* 针对反爬，检测到获取不到数据（需要输入验证码的页面），记录报错url
* 爬取完成后，针对报错url进行二次爬取
* 全部爬取完成后，执行sql导出为excel文件
