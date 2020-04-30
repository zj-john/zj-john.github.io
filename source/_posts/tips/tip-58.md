---
title: chrome，fetch发送请求时未传cookie
categories:
  - tips
tags:
  - chrome
  - fetch
  - cookie
toc: false
date: 2019-08-01 11:45:16
---

有一个工具碰到如下报障，转到我这里排查：

同一个web应用，在本地及大部分同事处调试良好，功能正常。但几个用户报障页面无数据，接口不能使用。

通过远程用户查看，发现后端获取数据的接口返回302。

> 背景：此项目是前后端分离，需要公司登录认证。前端经过认证后，埋下cookie，后端通过cookie二次校验是否登录。

从chrome dev tool 的network和抓包看，client确实没有把cookie传给后端。所以后端没有拿到登录信息，302到了登录页面。

![](/images/tips/fetch_cookie_3.png)

<!-- more -->

但比较奇怪的是：

1. get接口正常，cookie传给了后端
![](/images/tips/fetch_cookie_2.png)

2. post接口，没有带cookie。
![](/images/tips/fetch_cookie_1.png)

3. application面板看，cookie存在，且同源，且未过期。

因为报障用户的chrome版本是67，然后我本地的chrome67版本可以稳定复现故障现象，chrome69及以上版本则是好的。

初步确定是chrome版本差异导致的。  

问题的思路转化为：
1. chrome67 和 chrome69以上版本对cookie的处理有什么不同之处？

查阅了相关文档后，并未发现在对cookie的处理上有什么改动。毕竟cookie这种东西，受http的制约，不太敢瞎搞。

那从项目代码入手，  

前端请求接口使用的都是fetch，代码如下：
```js
PostHttp = (url, data) => {
    return new Promise(function (resolve, reject) {
        fetch(url, {
            body: JSON.stringify(data), // must match 'Content-Type' header
            method: 'POST', // *GET, POST, PUT, DELETE, etc.
            headers: {
                'content-type': 'application/json'
            },
        }).then((response) => {
            console.log("response", response);
            let data = (response && response.status === 200) ? response.json() : {
                "success": false,
                "msg": `接口异常，状态码为${response.status}`
            };
            resolve(data);
        }).catch((error) => {
            resolve({
                "success": false,
                "msg": `接口异常:${error}`
            });
        })
    });
};

GetHttp = (url) => {
    return new Promise(function (resolve, reject) {
        fetch(url).then((response) => {
            let data = (response && response.status === 200) ? response.json() : {
                "success": false,
                "msg": `接口异常，状态码为${response.status}`
            };
            resolve(data);
        }).catch((error) => {
            resolve({
                "success": false,
                "msg": `接口异常:${error}`
            });
        })
    });
};

```

fetch还是一个相对比较新的API，那问题转换为

2. 在chrome67 和 chrome69以上版本对cookie的处理有什么不同之处？

带着这些关键字一查找，还真的找到了文档上的一个细节：

在fetch文档sending cookie这一[章节](https://github.com/github/fetch#sending-cookies)下，看到了如下信息：  
![](/images/tips/fetch_cookie_4.png)

所以在chrome67版本前后，credentials的默认值是不同的。

让工具开发在fetch中，把credentials的值完整的写上去，发布再测试后，问题解决。

## 参考文档
1. https://github.com/github/fetch#sending-cookies
2. https://stackoverflow.com/questions/34558264/fetch-api-with-cookie
3. https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API/Using_Fetch