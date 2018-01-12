---
title: Chrome devtools-Elements面板
categories:
  - notes
tags:
  - note
  - Chrome
  - devtools
toc: true
date: 2017-12-26 10:41:45
---
chrome大概是最常用的浏览器之一，这篇笔记讲解下Chrome devtools中Elements面板的具体用法。  

[官方教程](https://developers.google.com/web/tools/chrome-devtools/)

<!-- more -->

## chrome 开发者工具

如果要使用最新版的devtools，可以安装[Chrome Canary](https://www.google.com/intl/en/chrome/browser/canary.html)版本.

### 打开方式：
* 在Chrome菜单中选择 更多工具 > 开发者工具
* 在页面元素上右键点击，选择 “检查”
* 使用 快捷键 Ctrl+Shift+I (Windows) 或 Cmd+Opt+I (Mac)

### 面板：
* 元素面板 Elements:  
使用元素面板可以自由的操作DOM和CSS来迭代布局和设计页面.

* 控制台面板 Console:  
在开发期间，可以使用控制台面板记录诊断信息，或者使用它作为 shell在页面上与JavaScript交互。

* 源代码面板 Source:   
在源代码面板中设置断点来调试 JavaScript ，或者通过Workspaces（工作区）连接本地文件来使用开发者工具的实时编辑器。

* 网络面板 Network:   
使用网络面板了解请求和下载的资源文件并优化网页加载性能。

* 性能面板 Performance:   
使用时间轴面板可以通过记录和查看网站生命周期内发生的各种事件来提高页面的运行时性能。
>在 Chrome 57 之前为时间线面板 Timeline

* 内存面板 Memory:  
如果需要比时间轴面板提供的更多信息，可以使用“配置”面板，例如跟踪内存泄漏。
>在 Chrome 57 之前为分析面板 Profiles

* 应用面板 Application:  
使用资源面板检查加载的所有资源，包括IndexedDB与Web SQL数据库，本地和会话存储，cookie，应用程序缓存，图像，字体和样式表。
>在 Chrome 52 之前为资源面板 Resources

* 安全面板 Security:  
使用安全面板调试混合内容问题，证书问题等等。

* 审计面板 Audits：  
对当前网页进行网络利用情况、网页性能方面的诊断，并给出一些优化建议。

## Elements面板
### 实时编辑DOM节点
* 编辑
* 移动
* 删除（Ctrl+Z 撤销）
* 滚动到视图： 要滚动页面使节点在视口中显示，请右键点击节点并选择 Scroll into View

### 实时编辑样式
Styles 窗格可以显示应用到选定元素的 CSS 规则，优先级从高到低。
* 已被级联规则替换的规则将显示为带删除线的文本
* 继承的样式将在“Inherited from <NODE>”标头下显示为一组。点击标头中的 DOM 节点可以导航到其在 DOM 树视图中的位置。
* 灰色的条目不是已定义的规则，而是在运行时计算的规则

#### 检查受选择器影响的元素
在 Styles 窗格中将鼠标悬停在 CSS 选择器上可以查看受该选择器影响的所有元素。
>此功能仅突出显示视口中的元素；视口以外的其他元素也可能受选择器影响。 （继承样式）

#### 添加、启用和停用 CSS 类
点击 .cls 按钮可以查看与当前选定元素关联的所有 CSS 类

#### 编辑现有属性名称或值
在名称或值突出显示时，按 Tab 可以向前移动到下一个属性、名称或选择器。
按 Shift+Tab 可以向后移动。

编辑数字式 CSS 属性值时，可以使用下面的键盘快捷键增大和减小值：
* 使用向上键和向下键能够以 1 为增量增大和减小值（如果当前值介于 -1 和 1 之间，则增量为 0.1）。
* 使用 Alt+向上键和 Alt+向下键能够以 0.1 为增量增大和减小值。
* 使用 Shift+向上键能够以 10 为增量增大值；使用 Shift+向下键能够以 10 为增量减小值。
* 使用 Shift+Page Up（Windows、Linux）或 Shift+Function+向上键 (Mac) * 能够以 100 为增量增大值。使用 Shift+Page Down（Windows、Linux）或 Shift+Function+向下键 (Mac) 能够以 100 为增量减小值。

#### 添加样式规则
点击 New Style Rule 按钮可以添加一个新的 CSS 规则。  
点击并按住按钮可以选择要向哪一个样式表添加规则。

#### 编辑 Sass、Less 或 Stylus
借助 CSS 源映射，DevTools 可以将生成的文件自动映射到源代码文件，这样，您可以在 Sources 面板中实时编辑这些文件并查看结果，而不用离开 DevTools 或刷新页面。  
默认情况下，CSS 源映射处于启用状态。启用/关闭操作：
1. 打开 DevTools 的 Settings 面板并点击 General。
2. 开启 Enable CSS source maps 和 Auto-reload generated CSS。

#### 检查和编辑框模型参数
使用 Computed 窗格检查和编辑当前元素的框模型参数。 框模型中的所有值均可修改，只需点击它们即可

#### 查看本地更改
1. 在 Styles 窗格中，点击您修改的文件。DevTools 会将您带到 Sources 面板。
2. 右键点击文件。
3. 选择 Local modifications。

粉色背景的线表示移除，绿色背景的线表示添加。

### DOM断点
如果您的 JavaScript 正在更改 DOM 元素的样式，请将 DOM 断点设置为在元素属性修改时触发。在发生以下一种 DOM 更改时触发断点：
* 子树更改
* 属性更改
* 节点移除

元素上右键选择Break on可以添加断点。  
DOM Breakpoints窗格：每个断点都会列出元素标识符和断点类型

### 元素事件侦听器
Event Listeners窗格：显示具有已注册侦听器的事件类型。
* handler  
包含一个回调函数。右键点击函数并选择 Show Function Definition 可以查看函数的定义位置（如果源代码可用）。

* useCapture  
指示 addEventListener 上的 useCapture 标志是否设置的布尔值。

> 许多 Chrome 扩展程序都会将其自己的事件侦听器添加到 DOM 上。如果您看到一些不是由您的代码设置的事件侦听器，您可能希望在隐身窗口中重新打开页面。默认情况下，隐身窗口会阻止扩展程序运行


选项：
* 查看祖先实体事件侦听器  
如果启用 Ancestors 复选框，除了当前选定节点的事件侦听器外，还会显示其祖先实体的事件侦听器

* 查看框架侦听器  
启用 Framework listeners 复选框时，DevTools 会自动解析事件代码的框架或内容库封装部分，然后告诉您实际将事件绑定到代码中的位置。  
如果停用 Framework listeners 复选框，事件侦听器代码很可能会在框架或内容库代码的其他地方解析。

> angular测试没有成功，jQuery可以找到定义源函数


### DevTools 的工作区设置持久化
[教程](https://developers.google.com/web/tools/setup/setup-workflow)
