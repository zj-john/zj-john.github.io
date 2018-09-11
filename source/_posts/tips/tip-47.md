---
title: wireshark报错failed to set hardware filter to promiscuous mode
categories:
  - tips
tags:
  - wireshark
  - promiscuous
toc: false
date: 2018-03-29 19:16:23
---

wireshark: failed to set hardware filter to promiscuous mode

在客户端抓包排查问题时，发现wireshark不能捕获无线网卡对应的流量信息，报failed to set hardware filter to promiscuous mode

<!-- more -->

解决方法：在Capture - Options 下把截图中的勾选去掉，即去掉混杂模式即可。  
![](/images/wireshark.jpg)

具体原理后续探索。
