---
title: 5分钟搭建属于自己的mock server平台
categories:
  - notes
tags:
  - mock server
  - service worker
toc: true
date: 2018-08-22 17:12:03
---

利用service worker 和 GitHub，5分钟搭建属于自己的mock server平台。

- **功能**：Mock server
- **工具**：servie worker, GitHub
- **难度等级**: ★★
- **项目地址**: https://github.com/zj-john/GitHubMockServerSample

<!-- more -->

## 功能
此项目配合[MyMockData](https://github.com/zj-john/MyMockData)一起使用。

我们做了什么：
* 把GitHub项目作为我们的mock server的数据源，例如[MyMockData](https://github.com/zj-john/MyMockData)
* 在项目中，通过service-worker.js，把数据源中的json数据，转化为json数据所描述的http响应，达到mock server的效果
* 可以通过json数据更改响应码、响应时间、响应头、校验post数据等功能

json举例：
```json
{
  "RequestMethod":["GET"],
  "ResponseHeaders":{},
  "StatusCode": "200",
  "Response": {
    "success": true,
    "message": "",
    "data": "This is a get mock data"
  },
  "ResponseTime": 1000
}
```

## 结构说明
```
│  .gitignore
│  README.md
│  service-worker.js
│
└─demo
    │  app.js
    │  package.json
    │
    ├─certificate
    │      ca.cer
    │      csr.pem
    │      private.pem
    │
    └─public
            index.html
            index.js
            service-worker.js
            styles.css
```

整个目录结构分为2部分，

### service-worker.js  

我们用于解析mock data的文件，mock data的组成参考[MyMockData](https://github.com/zj-john/MyMockData)项目。

在你的项目根文件(假设为index.html)中加入如下代码：
```html
<script>
  if ('serviceWorker' in navigator) {
    navigator.serviceWorker.register('service-worker.js').then(function(reg) {
      if(reg.installing) {
        console.log('Service worker installing');
      } else if(reg.waiting) {
        console.log('Service worker installed');
      } else if(reg.active) {
        console.log('Service worker active');
      }
    }).catch(function(error) {
      console.log('Registration failed with ' + error);
    });
  }
</script>
```
更精简的版本：
```html
<script>
  if ('serviceWorker' in navigator) {
    navigator.serviceWorker.register('service-worker.js');
  }
</script>
```

把service-worker.js 拷贝到项目根目录下即可。


### demo
demo文件夹下是一个可以运行的，基于node，使用https的web站点。此示例中可以看到mock server的完整用法。

[地址](https://zj-john.github.io/GitHubMockServerSample/demo/public/index.html)

#### chrome环境配置：  
本地运行的https是不安全的，所以要想service-worker能够成功运行，需要配置下浏览器的启动参数。

windows下启动参数配置如下:
```
C：\ Program Files（x86）\ Google \ Chrome \ Application \ chrome.exe --ignore-certificate-errors --unsafely-treat-insecure-origin-as-secure= https://localhost:8001
```

MacOS：
```
/ Applications / Google \ Chrome.app/Contents/MacOS/Google \ Chrome --user-data-dir = / tmp / foo --ignore-certificate-errors --unsafely-treat-insecure-origin-as-secure = https://localhost:8001
```

> 关于此配置的更多内容请参考：https://deanhume.com/testing-service-workers-locally-with-self-signed-certificates/
