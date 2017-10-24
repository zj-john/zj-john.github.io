---
title: 利用hexo、GitHub搭建自己的博客
date: 2017-09-12 14:06:28
categories:
  - projects
tags:
  - github
  - hexo
  - blog
  - yilla
toc: true
---
> Blog是提升逼格的利器。  

启用GitHub后，首先想到的便是利用GitHub的资源，搭建一下自己的博客。  
参考了网上的很多教程，也踩了很多坑，经验教训如下：

<!-- more -->

#### 1.GitHub新建repository ####

在GitHub填好各种资料，新建repository后，要拉取到本地进行开发。
- 配置ssh key：在GitHub上把本地的ssh添加进去
> Tips:网上很多教程上说需要在settings-GitHub Pages下用到Launch automatic page generator，但是这个按钮（功能）在新版的GitHub中已经不支持了，无需特殊配置.

- 在本地拉取文件，修改文件（比如：readme.md）后提交，成功说明GitHub步骤完成
> Tips: push origin master时可能会抛错：fatal: remote error:You can't push to git://github.com/my_user_name/my_repo.git Use git@github.com:my_user_name/my_repo.git会让修改源为https什么的，但是本地实践来看，[remote "origin"] url必须修改为	url = git@github.com:*** git的这个地址，https也不行。要修改的文件是在.git目录下config文件或者用命令git remote set-url origin git@github.com:my_user_name/my_repo.git。  
Tips:关心GitHub contribute的童鞋可能会发现，本地推到了源后，GitHub contribute上并未记录本次commit，需要把本地的git的邮箱放到GitHub的setting中，让GitHub知晓你本地git也是你。


#### 2.搭建hexo ####
搭建前后请一定阅读下以下文档:  
hexo中文官网：https://hexo.io/zh-cn/ （搭建前后各看一遍，理解更清楚）  
yilla的官网：https://github.com/litten/hexo-theme-yilia  （如果使用yilla主题,点击查看[效果](https://zj-john.github.io/) ）

>Tips: hexo是用npm安装的，如果报错：hexo时出现ERROR Deployer not found: Git此类报错，请单独安装其内部插件 如：npm install hexo-deployer-git –save

>Tips:可以把hexo init在你本地的文件目录下，如果报错文件夹不是空，可以随便找个地方init，然后整体搬过来即可，不影响后续使用。

>Tips:yilla主题是git clone过来的，如果不用git的submodel的话，提交会变的很麻烦。不是特别在yilla的版本和更新的话题的话，可以把yilla主题下的.git目录干掉，让它成为普通文件。

#### 2.hexo yilla配置 ####
hexo搭建后，开始修改各类配置文件，刚开始配置时很多项目不知道有什么用处，写起来会比较烦。建议找一个成熟的项目来看。  
推荐下自己的[blog项目](https://github.com/zj-john/zj-john.github.io)，里面master分支是hexo打包后的文件，没有什么有价值的内容。请看hexo分支，里面是hexo的源目录，hexo、yilla等配置都在里面，可以直接拿出来copy或研究。
