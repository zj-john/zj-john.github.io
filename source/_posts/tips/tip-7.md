---
title: 如何选择NPM依赖包版本号前缀
categories:
  - tips
tags:
  - tip
date: 2017-10-12 18:05:22
---
> 问题：如何选择NPM依赖包版本号前缀

<!-- more -->

跑别人的代码，发现有两个依赖包没有安装。准备配置到package.json中时发现依赖包版本前还有前缀，这都是啥意思。
```Javascript
"dependencies": {
  "ng-table": "^3.0.1"
},
"scripts": {
  "test": "gulp test",
  "start": "gulp serve",
  "build": "gulp serve:dist"
},
"devDependencies": {
  "angular-ui-router": "1.0.0-rc.1",
  "browser-sync": "~2.9.11",
  "browser-sync-spa": "~1.0.3"
}
```
参考回答：
[stackoverflow](https://stackoverflow.com/questions/22343224/whats-the-difference-between-tilde-and-caret-in-package-json)
[官网](https://docs.npmjs.com/misc/semver)

~和^的作用和区别：
* ~会匹配最近的小版本依赖包，比如~1.2.3会匹配所有1.2.x版本，但是不包括1.3.0
* ^会匹配最新的大版本依赖包，比如^1.2.3会匹配所有1.x.x的包，包括1.3.0，但是不包括2.0.0

当然，直接写前缀也没有任何问题。

>注意^版本更新可能比较大，会造成项目代码错误;使用~来标记版本号，这样可以保证项目不会出现大的问题，也能保证包中的小bug可以得到修复

除了~和^外，还有其他前缀可以使用，见下：
* version Must match version exactly
* \>version Must be greater than version
* \>=version etc
* <version
* <=version
* ~version "Approximately equivalent to version"
* ^version "Compatible with version"
* 1.2.x 1.2.0, 1.2.1, etc., but not 1.3.0
* http://... See 'URLs as Dependencies' below
* \* Matches any version
* "" (just an empty string) Same as *
* latest Obtains latest release
* version1 - version2 Same as >=version1 <=version2.
* range1 || range2 Passes if either range1 or range2 are satisfied.
* git... See 'Git URLs as Dependencies' below
* user/repo See 'GitHub URLs' below
* tag A specific version tagged and published as tag See npm-dist-tag
* path/path/path See Local Paths below

```
{ "dependencies" :
  { "foo" : "1.0.0 - 2.9999.9999"
  , "bar" : ">=1.0.2 <2.1.2"
  , "baz" : ">1.0.2 <=2.3.4"
  , "boo" : "2.0.1"
  , "qux" : "<1.0.0 || >=2.3.1 <2.4.5 || >=2.5.2 <3.0.0"
  , "asd" : "http://asdf.com/asdf.tar.gz"
  , "til" : "~1.2"
  , "elf" : "~1.2.3"
  , "two" : "2.x"
  , "thr" : "3.3.x"
  , "lat" : "latest"
  , "dyl" : "file:../dyl"
  , "npm" : "git+ssh://git@github.com:npm/npm.git#v1.0.27"
  }
}
```

讨论一个好玩儿的问题，当执行npm install <package> --save时，默认是使用~还是^呢？
从npm的merge上看，目前使用default to ^ instead of ~的标准。

有兴趣的可以看看类似的讨论：  
https://github.com/npm/npm/issues/4587   
https://github.com/npm/init-package-json/issues/10
