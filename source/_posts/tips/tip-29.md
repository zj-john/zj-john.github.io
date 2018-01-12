---
title: npm publish 报错：verified email required
categories:
  - tips
tags:
  - npm
  - email
  - verified
toc: true
date: 2018-01-10 11:50:37
---
> 问题：npm publish 报错：verified email required

发布自己的npm包时，执行npm publish有时候会报错,
```
npm ERR! publish Failed PUT 403
npm ERR! code E403
npm ERR! you must verify your email before publishing a new package: https://www.npmjs.com/email-edit : react-datatable-jq
```

<!-- more -->

## 解决方案

[官方教程](http://blog.npmjs.org/post/163187838935/upcoming-change-verified-email-required)

npm要求开发者提供了一个有效的电子邮件地址，并且在每次发布新的NPM包之前，需要验证你的电子邮件。

大概翻译如下：

### 原因
主要是为了防止垃圾邮件恶意批量注册和发布。

### 操作
当收到以上报错时：
1. 打开npm的[官网](https://www.npmjs.com/)
2. 登录
3. 看到以下图片所示banner
![](/images/npm_email.png)
4. 点击上图红字部分"send it again"，来发送一封验证邮件
5. 打开你的注册邮箱，找到验证邮件，点击邮件中的链接，确认验证

如果需要更换注册邮箱，请在[email edit地址](https://www.npmjs.com/email-edit)修改
