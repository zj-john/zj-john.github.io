---
title: git add -all 无法提交当前目录外的变动
categories:
  - tips
tags:
  - git
  - add
date: 2017-09-21 10:10:00
toc: true
---
> 问题：git add -all 无法提交当前目录外的变动

<!-- more -->

# 解决方法
在git2.0版本中，提交所有变动需要使用git add -all :/ 替代 git add -all，后者只能提交当前目录的变动。

```
warning: The behavior of 'git add --all (or -A)' with no path argument from a subdirectory of the tree will change in Git 2.0 and should not be used anymore.
To add content for the whole tree, run:

  git add --all :/
  (or git add -A :/)

To restrict the command to the current directory, run:

  git add --all .
  (or git add -A .)

With the current Git version, the command is restricted to the current directory
```
