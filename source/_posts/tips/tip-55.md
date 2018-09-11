---
title: chrome修改启动参数方式跨域
categories:
  - tips
tags:
  - chrome
  - 跨域
toc: false
date: 2018-08-10 12:55:58
---

之前一直用--disable-web-security的方式，取消chrome的同源策略，实现跨域。

chrome版本升级之后，发现不在生效。

查资料发现从chrome49版本后，如果想要实现相同的跨域效果，需要同时在启动项中加上--user-data-dir指定存储用户信息的目录。

> 开启--disable-web-security后，chrome处于不安全模式。不能使用默认的用户信息目录。也就是说，不指定--user-data-dir的话，无法读取任何用户信息，如cookie等。对需要此类信息的站点，将无法正常访问。

```
--disable-web-security :Don't enforce the same-origin policy. (Used by people testing their sites.)
--user-data-dir :Directory where the browser stores the user profile.
```

> 添加chrome启动参数的方法见参考。

# 参考：
* chrome官方说明：https://codereview.chromium.org/1512843002
* chrome启动参数：https://peter.sh/experiments/chromium-%20command-line-switches/
* 添加chrome启动参数的方法：https://www.cnblogs.com/laden666666/p/5544572.html
