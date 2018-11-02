---
title: Chrome devtools-Network面板
categories:
  - notes
tags:
  - Chrome
  - devtools
toc: true
date: 2017-12-29 10:31:20
---
chrome大概是最常用的浏览器之一，这篇笔记讲解下Chrome devtools中Network面板的具体用法。  

[官方教程](https://developers.google.com/web/tools/chrome-devtools/)

<!-- more -->

## Network面板
Network 面板记录页面上每个网络操作的相关信息，包括详细的耗时数据、HTTP 请求与响应标头和 Cookie，等等。

### 基本使用
#### Filters窗格：
使用这些选项（ALL JS CSS Img Media）可以控制在 Requests Table 中显示哪些资源。
>提示：按住 Cmd (Mac) 或 Ctrl (Windows/Linux) 并点击过滤器可以同时选择多个过滤器。

##### 文本字段关键词搜索
* domain：仅显示来自指定域的资源。您可以使用通配符字符 (\*) 来包含多个域。
* has-response-header：显示包含指定 HTTP 响应标头的资源。
* is：使用 is:running 可以查找 WebSocket 资源。
* larger-than：显示大于指定大小的资源（以字节为单位）。 将值设为 1000 等同于设置为 1k。
* method：显示通过指定 HTTP 方法类型检索的资源。
* mime-type：显示指定 MIME 类型的资源。DevTools 会使用它遇到的所有 MIME 类型填充下拉菜单。
* mixed-content：显示所有混合内容资源 (mixed-content:all)，或者仅显示当前显示的资源 (mixed-content:displayed)。
* scheme：显示通过未保护 HTTP (scheme:http) 或受保护 HTTPS (scheme:https) 检索的资源。
* set-cookie-domain：显示具有 Set-Cookie 标头并带有与指定值匹配的 Domain 属性的资源。
* set-cookie-name：显示具有 Set-Cookie 标头并且名称与指定值匹配的资源。
* set-cookie-value：显示具有 Set-Cookie 标头并且值与指定值匹配的资源。
* status-code：仅显示 HTTP 状态代码与指定代码匹配的资源。

#### Requests Table窗格：  
* Initiator：发起请求的对象或进程。值为以下选项之一：  

Parser: Chrome 的 HTML 解析器发起请求。  
Redirect: HTTP 重定向发起请求。  
Script: 脚本发起请求。  
Other: 某些其他进程或操作发起请求，例如用户通过链接或者在地址栏中输入网址导航到页面。

* Size：响应标头（通常为数百字节）加响应正文（由服务器提供）的组合大小。
* Time：从请求开始至在响应中接收到最终字节的总持续时间。

> use small request rows选项可以使Request 窗格显示详细或简单信息。
* size：详细时显示Size/Content，简单时显示Size
* Time：详细时显示Time/Latency，简单时显示Time  

其中Size是响应头部和响应体结合起来的大小，Content是请求内容解码后的大小（如果有缓存时size < content）；Time是从请求开始到接收到最后一个字节的总时长，Latency是从请求开始到接收到第一个字节的时间

##### Timeline列（Waterfall列）排序：
* Start Time：按每个网络请求的开始时间排序（默认排序）。
* Response Time：按每个请求的响应时间排序。
* End Time：按每个请求完成的时间排序。
* Duration：按每个请求的总时间排序。选择此过滤器可以确定哪些资源的加载时间最长。
* Latency：按请求开始与响应开始之间的时间排序。 选择此过滤器可以确定哪些资源至第一字节 (TTFB) 的时间最长。

#### 记录网络活动和捕捉屏幕截图
记录网络活动
* 红色：记录中
* 灰色：未记录

捕捉屏幕截图
* 蓝色：记录中
* 灰色：停用中

#### 查看 DOMContentLoaded 和 load 事件信息
DOMContentLoaded：当最初的HTML文档被完全加载和解析时触发（不等待样式、图片等）
* Overview 窗格中的蓝色竖线表示事件。
* 在 Summary 窗格中，您可以看到事件的确切时间

load：当页面完全加载时触发（资源及其相关资源完成）
* Overview 窗格中的红色竖线表示事件
* 在 Summary 窗格中，您可以看到事件的确切时间。

### 单个资源的详细信息

#### cookie标签
只有传输 Cookie 时，此标签才可用
* Domain：Cookie 所属的域。
* Path：Cookie 来源的网址路径。
* Expires / Max-Age：Cookie 的 expires 或 max-age 属性的值。
* Size：Cookie 的大小（以字节为单位）。
* HTTP：指示 Cookie 应仅由浏览器在 HTTP 请求中设置，而无法通过 JavaScript 访问。
* Secure：如果存在此属性，则指示 Cookie 应仅通过安全连接传输


#### Frames标签
只有选定资源发起 WebSocket 连接时，此标签才会显示。
* Data：消息负载。如果消息为纯文本，将在此处显示。 对于二进制操作码，此字段将显示操作码的名称和代码。 支持以下操作码：延续框架，二进制框架，连接关闭框架，Ping 框架，Pong 框架
* Length：消息负载的长度（以字节为单位）。
* Time：消息创建时的时间戳。

消息根据其类型进行彩色编码：
* 传出文本消息为浅绿色。
* 传入文本消息为白色。
* WebSocket 操作码为浅黄色。
* 错误为浅红色。

目前仅保留最后 100 条 WebSocket 消息

可以测试的[URL](http://chat.workerman.net/)

#### Timing标签
##### Resource Timing 的阶段
![](/images/resource-timing-api.png)

可以通过consolo中以下命令输出某个资源的Timing 信息
```
performance.getEntriesByType("resource").filter(item => item.name.includes(".css"))
```
##### 各阶段字段
* Queuing：如果某个请求正在排队，则指示：  

-请求已被渲染引擎推迟，因为该请求的优先级被视为低于关键资源（例如脚本/样式）的优先级。 图像经常发生这种情况  
-请求已被暂停，以等待将要释放的不可用 TCP 套接字。  
-请求已被暂停，因为在 HTTP 1 上，浏览器仅允许每个源拥有六个 TCP 连接。  
-生成磁盘缓存条目所用的时间（通常非常迅速）

* <span style="color:#cdcdcd">Stalled/Blocking</span>：  
请求等待发送所用的时间。 可以是等待 Queueing 中介绍的任何一个原因。 此外，此时间包含代理协商所用的任何时间。

* <span style="color:#cdcdcd">Proxy Negotiation</span>：  
与代理服务器连接协商所用的时间。

* <span style="color:#1f7c83">DNS Lookup</span>：  
执行 DNS 查询所用的时间。 页面上的每一个新域都需要完整的往返才能执行 DNS 查询。

* <span style="color:#e58226">Initial Connection / Connecting</span>：  
建立连接所用的时间，包括 TCP 握手/重试和协商 SSL 的时间。

* <span style="color:#e58226">SSL</span>：  
完成 SSL 握手所用的时间。

* <span style="color:#5fdd5f">Request Sent / Sending</span>：  
发出网络请求所用的时间。 通常不到一毫秒。

* <span style="color:#5fdd5f">Waiting (TTFB)</span>：  
等待初始响应所用的时间，也称为至第一字节的时间。 此时间将捕捉到服务器往返的延迟时间，以及等待服务器传送响应所用的时间。

* <span style="color:#4189d7">Content Download / Downloading</span>：  
接收响应数据所用的时间。

### 查看资源发起者和依赖项
按住 Shift 并将鼠标悬停在资源上，可以查看其发起者和依赖项。
* 绿色：发起者
* 红色：依赖项

悬停目标上方的第一个绿色编码资源为目标的发起者。 如果上方存在第二个也是绿色编码的资源，那么该资源将是发起者的发起者。 悬停目标下方红色编码的任何资源都是目标的依赖项。

> 对于具有大量资源的页面，您可能无法看到所有的发起者或依赖项。


### 复制、保存网络信息
* Copy Response：将选定资源的 HTTP 响应复制到系统剪贴板。
* Copy as cURL：以 cURL 命令字符串的形式将选定资源的网络请求复制到系统剪贴板。
* Copy All as HAR：以 HAR 数据形式将所有资源复制到系统剪贴板。HAR 文件包含用于说明网络“瀑布”的 JSON 数据结构。多款第三方 工具 可以依据 HAR 文件中的数据重建网络瀑布。
* Save as HAR with Content：将所有网络数据及每一个页面资源保存到 HAR 文件。 二进制资源（包括图像）以 Base64 编码文本的形式编码。
* Replay XHR。重新发送选定的 XMLHTTPRequest。仅在 XHR 资源上显示。

> cURL:用于进行 HTTP 事务的命令行工具[curl](https://curl.haxx.se/)



## 网络性能优化
### 网络问题
#### 已被加入队列或已被停止的系列
* HTTP 1.0/1.1 连接上，Chrome 会将每个主机强制设置为最多六个 TCP 连接。如果您一次请求十二个条目，前六个将开始，而后六个将被加入队列。最初的一半完成后，队列中的第一个条目将开始其请求流程。  
解决办法：设置多个子域。

* HTTP 2 中，到服务器的单个 TCP 连接作为多路复用连接。这消除了 HTTP 1 中的六个连接限制，并且可以通过单个连接同时传输多个资源

#### 至第一字节的漫长时间
Timing图大片绿色，等待时间长表示至第一字节的时间 (TTFB) 漫长。建议将此值控制在 200 毫秒以下。长 TTFB 会揭示两个主要问题：
* 客户端与服务器之间的网络条件较差
* 服务器应用的响应慢

#### 达到吞吐量能力
Timing图大片蓝色，Content Download 阶段花费了大量时间，则提高服务器响应或串联不会有任何帮助。首要的解决办法是减少发送的字节数

### 检查工具
[Insight](https://developers.google.com/speed/pagespeed/insights/)

### 规则
[Insights规则](https://developers.google.com/speed/docs/insights/rules)

### chrome中的优化
[high-performance-networking-in-google-chrome](https://www.igvita.com/posa/high-performance-networking-in-google-chrome/)

### gzip
[优化基于文本的资产的编码和传送大小](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/optimize-encoding-and-transfer)
