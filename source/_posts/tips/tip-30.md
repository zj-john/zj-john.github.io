---
title: npm publish 报错：You do not have permission to publish
categories:
  - tips
tags:
  - npm
  - permission
toc: true
date: 2018-01-11 12:23:42
---
> 问题：npm publish 报错：You do not have permission to publish

发布自己的npm包时，执行npm publish有时候会报错
```
npm ERR! publish Failed PUT 403
npm ERR! code E403
npm ERR! You do not have permission to publish "***". Are you logged in as the correct user?
```

<!-- more -->

## 解决方案
这个比较简单了，我自己都觉得这篇就是凑数的……

hava permission 没有权限，说明你发布的这个包在npm上已经被注册过了，你需要在package.json中改个名字。
