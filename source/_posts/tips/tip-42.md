---
title: 和版本升级有关的npm命令
categories:
  - tips
tags:
  - npm
toc: true
date: 2018-03-02 12:14:45
---

开发过程中，不可避免的遇到版本升级的问题。  
特别是协作的时候，如果忘记了package.json的更新，可能会出现难查的现象或BUG。

<!-- more -->
## npm outdated
查看当前目录下，所有插件的当前版本，package.json中的需要版本和该插件的最新版本
```
npm outdated
```
结果如下：
* Current: 当前项目中实际使用的版本
* Wanted：package.json中标注的版本
* Latest：插件的最新版本
* Location: 目录
<pre>
Package                             Current  Wanted  Latest  Location
react-big-calendar                  MISSING  0.18.0  0.18.0  myProject
babel-eslint                          8.2.1   8.2.2   8.2.2  myProject
bootstrap                             3.3.7   3.3.7   4.0.0  myProject
clipboard                             1.7.1   1.7.1   2.0.0  myProject
copy-webpack-plugin                   4.3.1   4.3.1   4.5.0  myProject
eslint-config-standard-react          5.0.0   5.0.0   6.0.0  myProject
eslint-config-standard        11.0.0-beta.0  11.0.0  11.0.0  myProject
eslint-plugin-promise                 3.6.0   3.7.0   3.7.0  myProject
eslint-plugin-react                   7.6.1   7.7.0   7.7.0  myProject
eslint                               4.16.0  4.18.2  4.18.2  myProject
file-loader                           1.1.6   1.1.6  1.1.11  myProject
react-bootstrap-typeahead             1.4.2   1.4.2   2.5.1  myProject
react-router                          3.2.0   3.2.1   4.2.0  myProject
redux                                 3.6.0   3.6.0   3.7.2  myProject
style-loader                         0.20.1  0.20.1  0.20.2  myProject
url-loader                            0.6.2   0.6.2   1.0.1  myProject
webpack-dev-server                   2.11.1  2.11.2   3.1.0  myProject
webpack                              3.10.0  3.11.0   4.1.1  myProject
babel-core                           6.26.0  linked  linked  myProject
babel-loader                          7.1.2  linked  linked  myProject
babel-polyfill                       6.26.0  linked  linked  myProject
babel-preset-env                      1.6.1  linked  linked  myProject
babel-preset-es2015                  6.24.1  linked  linked  myProject
classnames                            2.2.5  linked  linked  myProject
</pre>


## npm update
按照package.json的版本更新当前插件的版本
```js
npm update
// 对单个包升级
npm update <name>
```

## 可用工具
* [npm-check](https://www.npmjs.com/package/npm-check)
手动选择需要更新的包
* [npm-check-updates](https://www.npmjs.com/package/npm-check-updates)
根据package.json中的包的规则（^ ~等）自动把包提升到允许的最高版本
