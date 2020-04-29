---
title: React16 ReactRouter5特性
categories:
  - notes
tags:
  - JavaScript Weekly
  - 2018.09.14
toc: true
date: 2018-09-17 20:36:38
---


<!-- more -->

# React.StrictMode

create-react-app建立的demo项目中，使用了React.StrictMode

```
ReactDOM.render(
  <React.StrictMode>
    <Basic />
  </React.StrictMode>,
  document.getElementById('root')
);
```

## 特性
1. StrictMode 不会渲染任何可见的 UI。它为其后代元素触发额外的检查和警告。
2. 可以为应用程序的任何部分启用严格模式。

可以识别：
1. 识别不安全的生命周期
2. 关于使用过时字符串 ref API 的警告
3. 关于使用废弃的 findDOMNode 方法的警告
4. 检测意外的副作用
5. 检测过时的 context API

可以看出，这是一个帮助你进行react升级的好工具。

# Link 
以前的v2/v3版本
```
import { Link } from 'react-router'
```
从V4之后
```
import { Link } from 'react-router-dom'
```

# router
以前
```
import { hashHistory } from 'react-router'
const history = syncHistoryWithStore(hashHistory, store);
<Router history={history} routes={routes} />
```

现在
```
import {
  HashRouter as Router,
  Switch,
  Route,
  Link
} from "react-router-dom";

ReactDOM.render(
  <React.StrictMode>
    <Router>
      <Switch>
        <Route exact path="/">
          <Basic />
        </Route>
        <Route path="/about">
          <Food />
        </Route>
        <Route path="/dashboard">
          <Hotel />
        </Route>
      </Switch>
    </Router>
  </React.StrictMode>,
  document.getElementById('root')
);
```

# webpack

## clean-webpack-plugin
过去0.1.18/0.1.19
```
const CleanWebpackPlugin = require('clean-webpack-plugin');
new CleanWebpackPlugin(['*.*', 'js', 'scripts'], {
    root: path.resolve(__dirname, './dist'),
    verbose: true
})
```

现在^3.0.0
```
const { CleanWebpackPlugin } = require('clean-webpack-plugin');
new CleanWebpackPlugin({
  cleanAfterEveryBuildPatterns: ['build/home']
})
```

## css-loader

> Webpacker only supports css-loader versions greater than 2.1.1, and less than 3.0.0.