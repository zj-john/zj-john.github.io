---
title: react接入sentry
categories:
  - tips
tags:
  - react
  - sentry
toc: true
date: 2018-01-22 19:36:14
---
> 问题： react接入sentry

为了收集前端的报错，准备接入sentry系统。  
选择sentry的原因，首先这是是一款成熟的错误日志收集框架，支持多种语言以及细化的框架，比如支持JavaScript，也支持AngularJS、React、Vue等。
另一个原因则部门已经有一些其它语言，如Python在使用这个平台。

具体操作如下：
<!-- more -->

## 解决方案

### sentry配置
1. Sentry网站上注册后新建一个Project。
![](/images/sentry_react.png)
这里选择React框架，创建后会给你你的项目的客户端秘钥（DSN）
![](/images/sentry_finish.png)

2. 在React项目里安装raven
```
npm install raven-js --save
```

3. 项目中配置,config中的内容是第一步注册Project后得到的那个秘钥
```
import Raven from 'raven-js';
Raven.config('http://adagasdgasdgasdgas@sentry.com/10').install();
```

> 这两句代码放的位置可以随意，我推荐放在你项目的config.js中或入口文件，保证在执行其它catch语句前先执行install即可。放在config中，你可以根据相关配置（host、port）来决定什么环境再install，一般推荐生产环境再开启sentry。

执行到这里，sentry的配置部分就完成了，现在看具体使用。

### sentry使用
1. 请先升级react版本到16或16以后（包含react和react-dom两个组件都要升级到v16）
> react16发布了新功能，可以使用componentDidCatch方法很轻易的获取react中error。

2. 在具体的component中,按照如下方法即可把此component中的报错捕捉后发给sentry。

```js
import Raven from 'raven-js';

export default class Test extends React.Component {
    constructor (props) {
        super(props);
    }

    componentDidCatch(error, errorInfo) {
        Raven.captureException(error, { extra: errorInfo });
    }

    render() {

    }
}
```
> 目前看，只要在最外层的component上catch就可以把其内部使用的各个组件的error也catch住。具体还要再测试下。

### sentry优化
react一般使用webpack打包压缩，压缩后的报错文件可读性差，所以需要额外在sentry中配置map的文件。
