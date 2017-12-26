---
title: webpack devServer 中 proxy options
categories:
  - tips
tags:
  - webpack
  - devServer
  - proxy
toc: true
date: 2017-12-25 11:03:37
---
> 问题：webpack devServer中proxy options

webpack中DevServer中使用proxy来接收mock数据，一直是用的是localhost:port的数据，没有什么问题。如果使用了非localhost、非根path的数据，要进行特殊配置。  
配置如下：

<!-- more -->

## 实现原理
在webpack的官方devserver的[文档](https://webpack.js.org/configuration/dev-server/#devserver-proxy)中，并没有太多内容讲解proxy的用法。  
但是文档中告诉我们，这个部分时用了[http-proxy-middleware](https://github.com/chimurai/http-proxy-middleware#options)这个包,所以我们在这里找到了很详细的用法。

```js
devServer: {
    historyApiFallback: true,
    contentBase: path.join(__dirname, "dist"),
    port: 2200,
    hot: true,
    compress: false,
    publicPath: '/',
    stats: "minimal",
    proxy: {
        '/api/': {
            target: 'http://mywebsite.mockdata.com/',
            changeOrigin: true,
            secure: false,
            pathRewrite: {
                '^/api' : '/mock/8/api',
            }
        }
    }
},
```
参数用法：
* target: 'http://www.example.org', // target host
* changeOrigin: true,               // needed for virtual hosted sites
* ws: true,                         // proxy websockets
* pathRewrite:
```
{
    '^/api/old-path' : '/api/new-path',     // rewrite path
    '^/api/remove/path' : '/path'           // remove base path
}
```
* router:
```
{
    // when request.headers.host == 'dev.localhost:3000',
    // override target 'http://www.example.org' to 'http://localhost:8000'
    'dev.localhost:3000' : 'http://localhost:8000'
}
```
