---
title: windows 为CMD设置代理
categories:
  - tips
tags:
  - cmd
  - proxy
toc: false
date: 2018-05-02 14:36:55
---


在windows CMD中执行一些命令，由于是国内环境，很多地址可能访问不到(比如下载vagrant的插件)，我们需要配置代理。

<!-- more -->

设置方法如下：
```
set http_proxy=http://user:password@host:port
set https_proxy=%http_proxy%

```

通过set命令查看已有设置。
```
set | find "proxy"
```


> 设置后的代理只在本CMD中有效，其它cmd中查看set是没有prox的设置的。

参考文档：  

1. https://blog.csdn.net/rznice/article/details/50705451
2. https://stackoverflow.com/questions/19872591/how-to-use-vagrant-in-a-proxy-environment
