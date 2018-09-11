---
title: service worker
categories:
  - notes
tags:
  - service worker
toc: true
date: 2018-08-20 11:47:52
---

## Service Worker

Service worker是一个注册在指定源和路径下的事件驱动worker，本质上是WEB应用和浏览器，浏览器和网络之间的可编程代理

功能：  
* 创建有效的离线体验（细粒度地缓存资源） VS AppCache,前者更灵活
> 离线有必要吗？ 原生APP vs WebAPP

* 拦截并修改访问和资源请求
* 访问推送通知和后台同步API

特点：  
* 不能访问DOM
* 只能HTTPS
* 纯异步（同步API不可使用）

<!-- more -->

## 生命周期

### 下载
首次访问service worker控制的网站或页面时，service worker会立刻被下载。之后，至少每24小时它会被下载一次

### 安装
* 首次加载
* 与现有service worker不同（字节对比）

### 激活
* 现有service worker已启用，新版本会在后台安装，但不会被激活（worker in waiting）   
* 所有已加载的页面不再使用旧的service worker(当网站上当前打开的页面关闭时)才会激活新的service worker（active worker）


### 步骤
1. service worker URL 通过 serviceWorkerContainer.register() 来获取和注册。
2. 如果注册成功，service worker 就在 ServiceWorkerGlobalScope 环境中运行； 这是一个特殊类型的 woker 上下文运行环境，与主运行线程（执行脚本）相独立，同时也没有访问 DOM 的能力。
3. service worker 现在可以处理事件了。
4. 受 service worker 控制的页面打开后会尝试去安装 service worker。最先发送给 service worker 的事件是安装事件(在这个事件里可以开始进行填充 IndexDB和缓存站点资源)。这个流程同原生 APP 或者 Firefox OS APP 是一样的 — 让所有资源可离线访问。
5. 当 oninstall 事件的处理程序执行完毕后，可以认为 service worker 安装完成了。
6. 下一步是激活。当 service worker 安装完成后，会接收到一个激活事件(activate event)。 onactivate 主要用途是清理先前版本的service worker 脚本中使用的资源。
7. Service Worker 现在可以控制页面了，但仅是在 register()  成功后的打开的页面。也就是说，页面起始于有没有 service worker ，且在页面的接下来生命周期内维持这个状态。所以，页面不得不重新加载以让 service worker 获得完全的控制。（首次注册该服务工作线程的页面需要再次加载才会受其控制）


![](/images/sw-lifecycle.png)

## 使用场景
现在：
* 后台数据同步
* 响应来自其它源的资源请求
* 集中接收计算成本高的数据更新，比如地理位置和陀螺仪信息，这样多个页面就可以利用同一组数据
* 在客户端进行CoffeeScript，LESS，CJS/AMD等模块编译和依赖管理（用于开发目的）
* 后台服务钩子（反爬处理）
* 自定义模板用于特定URL模式
* 性能增强，比如预取用户可能需要的资源，比如相册中的后面数张图片

将来：
* 后台同步：启动一个service worker即使没有用户访问特定站点，也可以更新缓存
* 响应推送：启动一个service worker向用户发送一条信息通知新的内容可用
* 对时间或日期作出响应
* 进入地理围栏（用一个虚拟的栅栏围出一个虚拟地理边界）

## 事件
![](/images/sw-events.png)

## 常用接口
* Cache  
表示用于Request/Response对象对的存储，作为ServiceWorker生命周期的一部分被缓存。

* CacheStorage  
表示Cache对象的存储。提供一个所有命名缓存的主目录，ServiceWorker可以访问并维护名字字符串到Cache对象的映射。

* Client  
表示service worker client的作用域。一个service worker client可以是浏览器上下文的一个文档，也可以是一个由活动worker控制的SharedWorker。

* Clients  
表示一个Client对象容器，是访问当前源的活动service worker clients的主要途径。

* ExtendableEvent  
扩展被分发到ServiceWorkerGlobalScope的install和activate事件时序，作为service worker生命周期的一部分。这会确保任何功能型事件（如FetchEvent）不被分发到ServiceWorker，直到它更新了数据库架构、删除过期缓存项等等以后。

* FetchEvent  
传递给ServiceWorkerGlobalScope.onfetch处理函数的参数，FetchEvent代表一个在ServiceWorker的ServiceWorkerGlobalScope中分发的请求动作。它包含关于请求和响应的结果信息，并且提供FetchEvent.respondWith()方法，这个方法允许我们提供任意的响应返回到控制页面。

* InstallEvent  
传递给oninstall处理函数的参数，InstallEvent接口代表一个在ServiceWorker的ServiceWorkerGlobalScope中分发的安装动作，作为ExtendableEvent的子事件，它保证诸如FetchEvent 的功能性事件在安装过程中不会被分发。

* Navigator.serviceWorker  
返回一个ServiceWorkerContainer对象，可以提供入口用于注册，删除，更新以及与在相关 document中ServiceWorker通信的对象。

* NotificationEvent  
传递给onnotificationclick处理函数的参数，NotificationEvent 接口代表在ServiceWorker里ServiceWorkerGlobalScope中分发的单击事件通知。

* ServiceWorker  
表示一个service worker。多个浏览的上下文(例如pages,workers等等)都能通过相同的ServiceWorker对象相关联。

* ServiceWorkerContainer  
提供一个在网络生态中把service worker 作为一个整体的对象，包括辅助注册，反注册以及更新服务工作者，并且访问service worker 的状态以及他们的注册信息。

* ServiceWorkerGlobalScope  
表示service worker的全局执行上下文。

* ServiceWorkerMessageEvent  
包含关于一个发送给以navigator.serviceWorker为目标的事件信息。

* ServiceWorkerRegistration  
表示service worker的注册。

* WindowClient  
表示在浏览器上下文中记录的service worker客户端的作用域，被活动的工作者控制。是Client对象的特殊类型，包含一些附加的方法和可用的属性。

## 兼容性
Chrome(>42),Firefox(>44)已支持

* Chrome调试工具：chrome://serviceworker-internals/
* Firefox调试工具：about:serviceworkers

## DEMO

index.html
```js
// 特性检查
if ('serviceWorker' in navigator) {
  // 注册 这个文件的url 是相对于 origin， 而不是相对于引用它的那个 JS 文件;scope为sw的作用范围，最大的 scope 是 service worker 所在的地址
  navigator.serviceWorker.register('/sw.js', { scope: '/' }).then(function(reg) {
    if(reg.installing) {
      console.log('Service worker installing');
    } else if(reg.waiting) {
      console.log('Service worker installed');
    } else if(reg.active) {
      console.log('Service worker active');
    }
  }).catch(function(error) {
    // registration failed
    console.log('Registration failed with ' + error);
  });
}
```

### 缓存
sw.js
```js
self.addEventListener('install', function(event) {
  event.waitUntil(
    caches.open('v1').then(function(cache) {
      //addAll:参数是一个由一组相对于 origin 的 URL 组成的数组，这些 URL 就是你想缓存的资源的列表
      return cache.addAll([
        '/index.html',
        '/public/css/style.css',
        '/public/js/main.js',
        '/public/img/star-wars-logo.jpg',
        '/public/img/myLittleVader.jpg'
      ]);
    })
  );
});

// 缓存处理
self.addEventListener('fetch', function(event) {
  //允许我们对网络请求的资源和 cache 里可获取的资源进行匹配，查看是否缓存中有相应的资源。这个匹配通过 url 和 vary header进行
  event.respondWith(caches.match(event.request).then(function(response) {
    if (response !== undefined) {
      return response;
    } else {
      return fetch(event.request).then(function (response) {
        // response may be used only once
        // we need to save clone to put one copy in cache
        // and serve second one
        let responseClone = response.clone();
        // 添加新缓存
        caches.open('v1').then(function (cache) {
          cache.put(event.request, responseClone);
        });
        return response;
      }).catch(function () {
        return caches.match('/default.jpg');
      });
    }
  }));
});
```

### 更改请求、响应

```js
// 请求处理
self.addEventListener('fetch', function(event) {
  event.respondWith(
    fetch(event.request).then(function(response) {
        return response;
    }).catch(function() {
        var request = event.request;
        // 判断资源类型
        if(request.destination==='script') {
          // 请求jQuery的资源报错，进行地址更换
          if(request.url.indexOf('jquery')>-1){
            var backupUrl = "https://code.jquery.com/jquery-1.12.4.min.js";
            let backupRequest = new Request(backupUrl, {
              method: request.method,
              headers: request.headers,
              credentials: request.credentials,
              redirect: 'manual',
              //从不支持 CORS 的第三方网址中获取资源
              mode: 'no-cors'
            });
            return fetch(backupRequest);
          }
        }
        return new Response('<p>Resource Error!</p>', {
          headers: { 'Content-Type': 'text/html' }
        })
    })
  );
});
```

### 更新service worker
```js
// activate 清理过期sw数据
self.addEventListener('activate', function(event) {
  var cacheWhitelist = ['v2'];
  event.waitUntil(
    caches.keys().then(function(keyList) {
      return Promise.all(keyList.map(function(key) {
        if (cacheWhitelist.indexOf(key) === -1) {
          return caches.delete(key);
        }
      }));
    })
  );
});
```

### 单向通信
index.html
```js
function oneWayCommunication() {
  if (navigator.serviceWorker.controller) {
    navigator.serviceWorker.controller.postMessage({
      command: 'oneWayCommunication',
      message: 'Hi, SW'
    });
  }
}
```

```js
self.addEventListener('message', function(event) {
  const data = event.data;
  if (data.command === 'oneWayCommunication') {
    console.log(`Message from the Page : ${data.message}`);
  }
});
```

### 双向通信
index.html
```js
function twoWayCommunication() {
  if (navigator.serviceWorker.controller) {
    const messageChannel = new MessageChannel();
    messageChannel.port1.onmessage = function(event) {
      console.log(`Response from the SW : ${event.data.message}`);
    }
    navigator.serviceWorker.controller.postMessage({
      command: 'twoWayCommunication',
      message: 'Hi, SW'
    }, [messageChannel.port2]);
  }
}
```

```js
self.addEventListener('message', function(event) {
  const data = event.data;
  if (data.command === 'twoWayCommunication') {
    event.ports[0].postMessage({
      message: 'Hi, Page'
    });
  }
});
```


### 广播
index.html
```js
function registerBroadcastReceiver() {
  navigator.serviceWorker.onmessage = function(event) {
    const data = event.data;
    if (data.command === 'broadcastOnRequest') {
      console.log(`Broadcasted message from the ServiceWorker : ${data.message}`);
    }
  };
}

function requestBroadcast() {
  registerBroadcastReceiver();
  if (navigator.serviceWorker.controller) {
    navigator.serviceWorker.controller.postMessage({
      command: 'broadcast'
    });
  }
}
```

sw.js
```js
self.addEventListener('message', function(event) {
  const data = event.data;
  if (data.command === 'broadcast') {
    self.clients.matchAll().then(function(clients) {
      clients.forEach(function(client) {
        client.postMessage({
          command: 'broadcastOnRequest',
          message: 'This is a broadcast on request from the SW'
        });
      })
    })
  }
});
```


## 参考文档
* W3C: https://w3c.github.io/ServiceWorker/
* Cookbook：https://serviceworke.rs/
* https://developer.mozilla.org/zh-CN/docs/Web/API/Service_Worker_API
* https://developer.mozilla.org/zh-CN/docs/Web/API/Service_Worker_API/Using_Service_Workers
* https://googlechrome.github.io/samples/service-worker/
* https://zhuanlan.zhihu.com/p/27264234
