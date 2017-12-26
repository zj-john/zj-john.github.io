---
title: 规范化 git commit
categories:
  - tips
tags:
  - image
  - node
  - base64
toc: true
date: 2017-12-19 10:33:47
---
> 问题：规范化 git commit

更改开源项目中，发现commit时有限制，需要按照规范的格式提交。  
为了引入到自己的项目中，了解下实现原理。

<!-- more -->

## 实现原理
直接看代码：  
package.json

```json
  "devDependencies": {
    "validate-commit-msg": "^2.12.2"
  },
  "config": {
    "ghooks": {
      "commit-msg": "validate-commit-msg"
    },
    "validate-commit-msg": {
      "types": [
        "feat",
        "fix",
        "docs",
        "test",
        "chore",
        "refactor",
        "opti"
      ],
      "warnOnFail": false,
      "maxSubjectLength": 100,
      "subjectPattern": ".+",
      "subjectPatternErrorMsg": "请输入message信息!",
      "helpMessage": "Commit message 格式错误, \n请查看规范: https://github.com/conventional-changelog-archived-repos/validate-commit-msg"
    }
  }
```
完成以上配置后，则只可以通过以下格式提交：
```json
<type>(<scope>): <subject>
// or
<type>: <subject>
```


使用了[validate-commit-msg](https://github.com/conventional-changelog-archived-repos/validate-commit-msg)这个插件，相关参数说明：
* types: These are the types that are allowed for your commit message. "types": "\*" to indicate that you don't wish to validate types.
* warnOnFail: If this is set to true errors will be logged to the console, however the commit will still pass.
* subjectPattern: Optional, accepts a RegExp to match the commit message subject against.
* scope: This object defines scope requirements for the commit message

> 使用规范化的commit可以便于查找问题已经生产change log


后续学习可以参考[Commit message 和 Change log 编写指南](http://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html)
