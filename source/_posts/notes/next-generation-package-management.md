---
title: 下一代包管理工具tink
categories:
  - notes
tags:
  - JavaScript Weekly
  - tink
  - npm
  - 2018.09.14
toc: true
date: 2018-09-17 20:30:02
---
如果想要只用node，就可以让安装依赖快速到可以放到后台运行，而不用把进度显示在界面，该怎么办？  
如果想要依赖项中的每个文件都能确保和你注册它们时一模一样，该怎么办？
如果依赖项中的每个文件都可以保证与注册表中的文件一点一点怎么办？如果在新项目上工作就像克隆和运行一样简单怎么办？如果你的构建工具不合适怎么办？


<!-- more -->

本文介绍[`tink`](https://github.com/npm/tink)一个无安装安装程序的概念验证实现。

`tink`作为Node.js本身的替代品，使用现有的`package-lock.json`。在没有a的项目上试用它`node_modules`，你会发现`require`即使你从未运行过安装，你仍然可以使用任何依赖项。第一次运行将在下载并提取包tar包时花费几秒钟，但后续运行几乎是即时的，即使它仍在验证`package-lock.json`您的系统中的所有内容。

你会注意到的第一件事是这些模块都没有放入你的`node_modules`文件夹 \- 事实上，你唯一能找到的就是一个`.package-map.json`文件。这包含已安装的每个包中所有文件的哈希值。这些在加载之前就已经过验证，因此您可以放心地获得所要求的内容（如果验证失败，则文件将从其原始源获取，全部透明）。

我们不会把婴儿扔出洗澡水。您仍然可以在`node_modules`文件夹中安装内容，这些版本将优先于缓存版本使用。这为依赖项的实时编辑（有时是必要的调试技术）打开了一条路径，并支持改变软件包分发的postinstall脚本。

`tink`是一个改变我们与Node.js项目和npm Registry相关的方式的机会。是否应该使用`require`或`import`不在您的模块`package.json`中将其添加到您的`package.json`？默认情况下，是否应该提供非常受欢迎的功能，例如与babel兼容的esm，typescript或jsx？这些是我们一直在问自己的问题，我们很想听听您对下一代体验的期望。请来[npm.community](https://t.umblr.com/redirect?z=https%3A%2F%2Fnpm.community&t=MzE1YThiMDY5NDdlM2U2ZGExZGJjYWQwODYzZjJmMjI5NTkzNThlYix2SGt2amVPVg%3D%3D&b=t%3AnXsLs1P4AptPf1fBr_nFxw&p=https%3A%2F%2Fblog.npmjs.org%2Fpost%2F178027064160%2Fnext-generation-package-management&m=1)告诉我们！

## 资源
* 原文 https://blog.npmjs.org/post/178027064160/next-generation-package-management
