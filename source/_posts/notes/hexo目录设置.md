---
title: hexo yilia主题的优化-目录篇
categories:
  - notes
tags:
  - hexo
  - yilia
  - 优化
toc: true
date: 2017-10-25 11:06:48
---

# 简介

一个帅气的博客应该具有：
* 文章目录
* 文章评论功能
* 主页显示时，文章的截断功能
* 博客的访客、浏览量的统计  

本篇讲解 <b>文章目录</b> 的设置方法。

<!-- more -->

# 设置方法
## 修改模板
目录是针对文章来说的，所以对文章添加目录，需要修改默认的文章模板。  
对于yilia主题，文章的模板是<font style='background-color:silver;'>themes\yilia\layout\\_partial\article.ejs</font>文件。在该文件中添加的内容和位置如下：

```javascript
···
<% if (post.link || post.title){ %>
  <header class="article-header">
    <%- partial('post/title', {class_name: 'article-title'}) %>
    <% if (!post.noDate){ %>
    <%- partial('post/date', {class_name: 'archive-article-date', date_format: null}) %>
    <% } %>
  </header>
<% } %>
<!-- 目录内容 -->
<% if (!index && post.toc){ %>
  <p class="show-toc-btn" id="show-toc-btn" onclick="showToc();" style="display:none">
    <span class="btn-bg"></span>
    <span class="btn-text">文章导航</span>
  </p>
  <div id="toc-article" class="toc-article">
    <span id="toc-close" class="toc-close" title="隐藏导航" onclick="showBtn();">×</span>
    <strong class="toc-title">文章目录</strong>
     <%- toc(post.content) %>
   </div>
   <script type="text/javascript">
      function showToc(){
        var toc_article = document.getElementById("toc-article");
        var show_toc_btn = document.getElementById("show-toc-btn");
        toc_article.setAttribute("style","display:block");
        show_toc_btn.setAttribute("style","display:none");
        };
      function showBtn(){
        var toc_article = document.getElementById("toc-article");
        var show_toc_btn = document.getElementById("show-toc-btn");
        toc_article.setAttribute("style","display:none");
        show_toc_btn.setAttribute("style","display:block");
        };
     </script>
<% } %>
<!-- 目录内容结束 -->
<div class="article-entry" itemprop="articleBody">
···
```

## 添加CSS样式
添加的模板内容包括HTML和JS，现在需要为HTML添加一个合适的CSS，例如浮动、缩进等效果。  
CSS样式需要添加在<font style='background-color:silver;'>themes\yilia\source \main.\*\*\*\*\*\*.css</font>文件中（此文件是yilia源文件压缩后的文件，不同的yilia版本\*\*\*\*\*\*的内容会有所区别）。
在该文件末尾添加如下内容：
```
/* 目录css */
#container .show-toc-btn,#container .toc-article {
	display: block
}

.toc-article {
	z-index: 100;
	background: #fff;
	border: 1px solid #ccc;
	max-width: 250px;
	min-width: 150px;
	max-height: 500px;
	overflow-y: auto;
	-webkit-box-shadow: 5px 5px 2px #ccc;
	box-shadow: 5px 5px 2px #ccc;
	font-size: 12px;
	padding: 10px;
	position: fixed;
	right: 35px;
	top: 129px
}

.toc-article .toc-close {
	font-weight: 700;
	font-size: 20px;
	cursor: pointer;
	float: right;
	color: #ccc
}

.toc-article .toc-close:hover {
	color: #000
}

.toc-article .toc {
	font-size: 12px;
	padding: 0;
	line-height: 20px
}

.toc-article .toc .toc-number {
	color: #333
}

.toc-article .toc .toc-text:hover {
	text-decoration: underline;
	color: #2a6496
}

.toc-article li {
	list-style-type: none
}

.toc-article .toc-level-1 {
	margin: 4px 0
}

.toc-article .toc-child {
}
/* cd-bounce-1在main******.css中文件有设置，这里是做效果替换 */
@-moz-keyframes cd-bounce-1 {
	0% {
		opacity: 0;
		-o-transform: scale(1);
		-webkit-transform: scale(1);
		-moz-transform: scale(1);
		-ms-transform: scale(1);
		transform: scale(1)
	}

	60% {
		opacity: 1;
		-o-transform: scale(1.01);
		-webkit-transform: scale(1.01);
		-moz-transform: scale(1.01);
		-ms-transform: scale(1.01);
		transform: scale(1.01)
	}

	100% {
		-o-transform: scale(1);
		-webkit-transform: scale(1);
		-moz-transform: scale(1);
		-ms-transform: scale(1);
		transform: scale(1)
	}
}

@-webkit-keyframes cd-bounce-1 {
	0% {
		opacity: 0;
		-o-transform: scale(1);
		-webkit-transform: scale(1);
		-moz-transform: scale(1);
		-ms-transform: scale(1);
		transform: scale(1)
	}

	60% {
		opacity: 1;
		-o-transform: scale(1.01);
		-webkit-transform: scale(1.01);
		-moz-transform: scale(1.01);
		-ms-transform: scale(1.01);
		transform: scale(1.01)
	}

	100% {
		-o-transform: scale(1);
		-webkit-transform: scale(1);
		-moz-transform: scale(1);
		-ms-transform: scale(1);
		transform: scale(1)
	}
}

@-o-keyframes cd-bounce-1 {
	0% {
		opacity: 0;
		-o-transform: scale(1);
		-webkit-transform: scale(1);
		-moz-transform: scale(1);
		-ms-transform: scale(1);
		transform: scale(1)
	}

	60% {
		opacity: 1;
		-o-transform: scale(1.01);
		-webkit-transform: scale(1.01);
		-moz-transform: scale(1.01);
		-ms-transform: scale(1.01);
		transform: scale(1.01)
	}

	100% {
		-o-transform: scale(1);
		-webkit-transform: scale(1);
		-moz-transform: scale(1);
		-ms-transform: scale(1);
		transform: scale(1)
	}
}

@keyframes cd-bounce-1 {
	0% {
		opacity: 0;
		-o-transform: scale(1);
		-webkit-transform: scale(1);
		-moz-transform: scale(1);
		-ms-transform: scale(1);
		transform: scale(1)
	}

	60% {
		opacity: 1;
		-o-transform: scale(1.01);
		-webkit-transform: scale(1.01);
		-moz-transform: scale(1.01);
		-ms-transform: scale(1.01);
		transform: scale(1.01)
	}

	100% {
		-o-transform: scale(1);
		-webkit-transform: scale(1);
		-moz-transform: scale(1);
		-ms-transform: scale(1);
		transform: scale(1)
	}
}

.show-toc-btn {
	display: none;
	z-index: 10;
	width: 30px;
	min-height: 14px;
	overflow: hidden;
	padding: 4px 6px 8px 5px;
	border: 1px solid #ddd;
	border-right: none;
	position: fixed;
	right: 40px;
	text-align: center;
	background-color: #f9f9f9
}

.show-toc-btn .btn-bg {
	margin-top: 2px;
	display: block;
	width: 16px;
	height: 14px;
	background: url(http://7xtawy.com1.z0.glb.clouddn.com/show.png) no-repeat;
	-webkit-background-size: 100%;
	-moz-background-size: 100%;
	background-size: 100%
}

.show-toc-btn .btn-text {
	color: #999;
	font-size: 12px
}

.show-toc-btn:hover {
	cursor: pointer
}

.show-toc-btn:hover .btn-bg {
	background-position: 0 -16px
}

.show-toc-btn:hover .btn-text {
	font-size: 12px;
	color: #ea8010
}

.toc-article li ol, .toc-article li ul {
	margin-left: 30px;
}

.toc-article ol, .toc-article ul {
	margin: 10px 0;
}

```
可以压缩后再添加。

## 文章中添加设置
在需要添加目录的文章中，开头添加toc: true.  
如本篇：
```
title: hexo yilia主题的优化-目录篇
categories:
  - notes
tags:
  - hexo
  - yilia
  - 优化
toc: true
date: 2017-10-25 12:06:48
```
文章的目录是根据markdown中的#标识来区别的，即h1(\#)到h6(\#\#\#\#\#\#)
