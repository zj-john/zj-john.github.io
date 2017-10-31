---
title: hexo yilia主题的优化-评论篇
categories:
  - notes
tags:
  - hexo
  - yilia
  - 优化
toc: true
date: 2017-10-26 12:06:48
---

# 简介

一个帅气的博客应该具有：
* 文章目录
* 文章评论功能
* 主页显示时，文章的截断功能
* 博客的访客、浏览量的统计  

本篇讲解 <b>文章评论</b> 的设置方法。

<!-- more -->

# 设置方法
## 选择第三方插件
yilia主题里默认提供了几种插件的支持，其中：  
1. 多说、网易云跟帖已经关闭
2. 畅言需要域名备案才可使用
3. disqus在国内由于你懂的原因,基本上不能正常使用
4. Gitment设置、维护比较复杂，且只支持GitHub账号  

看起来，基本上都处于不能用的状态。所以最终选用的是[来必力](https://livere.com/city-demo)这个插件。来必力安装使用方便，且提供管理、统计界面。

使用的话，注册一下，然后选择个人使用的免费版本City版本，选择“现在安装”，会到代码管理界面，如下：
![](/images/lbili.jpg)


## 设置yilia 配置文件
打开yilia的配置文件:<font style='background-color:silver;'>\themes\yilia\_config.yml</font>,在其中加入内容，见下：

```
#评论：1、多说；2、网易云跟帖；3、畅言；4、Disqus；5、Gitment
#不需要使用某项，直接设置值为false，或注释掉
#具体请参考wiki：https://github.com/litten/hexo-theme-yilia/wiki/

#1、多说
duoshuo: false

#2、网易云跟帖
wangyiyun: false

#3、畅言
changyan_appid: false
changyan_conf: false

#4、Disqus 在hexo根目录的config里也有disqus_shortname字段，优先使用yilia的
disqus: false

#5、Gitment
gitment_owner: false      #你的 GitHub ID
gitment_repo: ''          #存储评论的 repo
gitment_oauth:
  client_id: ''           #client ID
  client_secret: ''       #client secret

#6 来必力 把代码管理中 data-uid中的内容复制到这里。
livere_uid: "*************"

```

## 配置模板文件
打开页面模板文件：<font style='background-color:silver;'>themes\yilia\layout\\_partial\article.ejs</font>，在文件末尾如下位置，添加来必力代码管理中心的代码。

先判断配置文件中是否配置了来必力。配置方法见上一小节。
```
<% if (theme.livere_uid){ %>
...
<% } %>
```

为了评论框的效果，我们在代码上层添加了duoshuo的section样式:
```
<section class="duoshuo" id="comments"></section>
```
整体代码如下：

```
<% if (theme.gitment_owner && theme.gitment_repo &&theme.gitment_oauth && theme.gitment_oauth.client_id && theme.gitment_oauth.client_secret){ %>
<%- partial('post/gitment', {
    key: post.slug,
    title: post.title,
    url: config.url+url_for(post.path)
  }) %>
<% } %>

<!-- 来必力City版安装代码 -->
<% if (theme.livere_uid){ %>
  <!-- 这一句是适配样式 -->
  <section class="duoshuo" id="comments">
    <!-- 以下是 来必力City版安装代码 -->
    <div id="lv-container" data-id="city" data-uid="MTAyMC8zMTQ5MS84MDU1">
      <script type="text/javascript">
       (function(d, s) {
           var j, e = d.getElementsByTagName(s)[0];

           if (typeof LivereTower === 'function') { return; }

           j = d.createElement(s);
           j.src = 'https://cdn-city.livere.com/js/embed.dist.js';
           j.async = true;

           e.parentNode.insertBefore(j, e);
       })(document, 'script');
      </script>
    <noscript> 为正常使用来必力评论功能请激活JavaScript</noscript>
    </div>
  </section>
<% } %>
```

完成上述配置后，就可以看到效果啦：
![](/images/pinglun.jpg)

## 关闭评论方法
这里的设置会对所有的文章生效。如果想要关闭评论功能要怎么办呢？  
只需要在刚才设置判断条件的地方多加一个判断条件，如下代码所示。然后在不需要评论的文章head中加comment: false，在需要加评论的文章head中加comment: true即可.

 ```
 <% if (theme.livere_uid && post.comment){ %>
 ...
 <% } %>
 ```
（^_^ 看到这里，你对hexo的实现原理是不是也多了些了解呢）

>Tips：如果开启了此设置，但需要开启评论的文章数又很多。可以在文章的template文件（scaffolds文件夹下）中把comment：true加上，这样再使用hexo new命令创建的文件默认就开启了评论。
