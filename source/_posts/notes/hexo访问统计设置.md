---
title: hexo yilia主题的优化-访问统计篇
categories:
  - notes
tags:
  - hexo
  - yilia
  - 优化
toc: true
date: 2017-10-27 11:31:48
---

# 简介

一个帅气的博客应该具有：
* 文章目录
* 文章评论功能
* 主页显示时，文章的截断功能
* 博客的访客、浏览量的统计  

本篇讲解 <b>文章的截断功能和访问、浏览统计</b> 的设置方法。

<!-- more -->

# 截断功能
默认情况博客的主页上的文章会全文显示，所以主页就显得非常臃肿。如何把主页上的文章截断，只展示一部分内容呢：  
实现非常简单，在文章md源文件中需要截断的地方加上如下代码。加上后，文章内容会在该代码的位置进行截断，主页上只显示该代码上部的内容。
```
<!-- more -->
```

# 访问、浏览统计
## 不蒜子插件
实现网站访问、浏览统计的第三方插件有很多，Amazon、baidu、Google都有提供，大家可以根据需要自己去搜索。我这里用的是国内开发人员“不如”开发的不蒜子插件。  
插件的具体原理、实现方法、扩展开发可以参看插件[官方页面](http://ibruce.info/2015/04/04/busuanzi/)。  
如果只是使用的话，方法非常简单，可以跳过文档，直接看下一步。

## 配置方法
本文我们把访问统计放在博客的页面底部，所以需要修改对应的模板文件：
<font style='background-color:silver;'>themes\yilia\layout\\_partial\footer.ejs</font>。  

修改后的文件见下：
```

<footer id="footer">
  <div class="outer">
    <div id="footer-info">
    	<div class="footer-left">
    		&copy; <%= date(new Date(), 'YYYY') %> <%= config.author || config.title %>
    	</div>
    	<div class="footer-right">
    		<a href="http://hexo.io/" target="_blank">Hexo</a>  Theme <a href="https://github.com/litten/hexo-theme-yilia" target="_blank">Yilia</a> by Litten
    	</div>
    </div>
  </div>
  <!-- 不蒜子内容 Start-->
  <script async src="//dn-lbstatics.qbox.me/busuanzi/2.3/busuanzi.pure.mini.js"></script>
  <span id="busuanzi_container_site_pv" style="display: inline;">
    本站总访问量<span id="busuanzi_value_site_pv"></span>次
  </span>
  <span id="busuanzi_container_site_uv" style="display: inline;">
    总访客数<span id="busuanzi_value_site_uv"></span>人次
  </span>
  <!-- 不蒜子内容 End -->
</footer>

```



>Tip: 这里把不蒜子的内容放在footer tag内，是为了把footer的样式用在不蒜子中。如果自己写样式的话，只要保持不蒜子内容的完整，代码放置位置就随意了。

最终实现的效果：
![](/images/fangke.jpg)
