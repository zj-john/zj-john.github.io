---
title: Electron再见, Desktop PWAs你好
categories:
  - notes
tags:
  - JavaScript Weekly
  - Web Development
  - PWA
  - Desktop App
  - Electron
  - 2018.11.16
toc: true
date: 2018-11-21 10:36:50
---

![](/images/JS_Weekly/goodbye-electron-hello-desktop-pwas/1_xb103Yfo58QRyISMB44bBg.png)

<!-- more -->

### Chrome v70最近的公告
最新的Chrome版本（v70）增加了Linux和Windows上对Desktop Progressive Web应用程序（DPWA）的支持，这令人兴奋。下图是Chrome具体的支持列表。

![](/images/JS_Weekly/goodbye-electron-hello-desktop-pwas/1_CG8V1ORNFb-t9-R17OnbDA.png)

列表里没有MacOS，不过不用担心，Chrome V72中应该会添加这一支持。

请注意，虽然默认情况下MacOS不提供此功能，但可通过Chrome的功能标记启用。要启用这些标记，只需打开Chrome，并转到[chrome://flags](about:invalid)或直接转到Desktop PWAs的选项[chrome://flags/#enable-desktop-pwas](about:invalid)。启用任意功能标记后记得重启Chrome生效。

### 什么是渐进式Web应用程序（PWA）？
这里我们不会深入介绍PWA，您可以阅读[Google的文档](https://developers.google.com/web/progressive-web-apps/)来学习。如果想查看一些丰富的示例并了解在Android的Chrome，iOS的Safari（部分支持）上使用PWA的感觉，可以阅读这些[示例](https://medium.com/@the_manifest/11-examples-of-progressive-web-apps-944f6db25a5a)。

### 什么是Electron？
[Electron](https://electronjs.org/) 是一个允许开发人员使用Javascript，HTML和CSS构建跨平台桌面应用的平台。  
它是一种替代方案，降低了Web应用开发人员创建桌面程序的门槛，也让一些优秀的Javascript库和框架有了新的应用领域。  
基于Electron的桌面应用程序十分风靡，Slack，VSCode，Atom和Discord等项目在Electron开发方面都投入了大量资金。如果您还没有使用过这些服务，请试一试。

桌面应用程序一向以笨重的和过时的UI被人诟病，Electron的出现为桌面领域带来了一缕清泉！


### 为什么选择桌面PWAs？
尽管Electron应用程序越来越多，并且它们的实现与Web应用程序的实现接近，但它们仍然存在一些缺点。  
首先，Electron应用程序附带了他们自己的Chromium二进制文件。我们知道运行Chrome是很费内存的，同时使用多个Electron应用程序对内存简直是个灾害。如果您有所怀疑，可以在您的机器上打开多个Electron应用程序，然后通过您的活动监视器监视下内存的使用。  
除了对内存的大量耗费导致效率低下外，许多Electron应用程序还需要与NodeJS对接的额外的Electron代码接口，并且不一定与其相应的Web应用程序相同。通过必须管理对旧版本和软件更新管道的支持，Electron还需要额外的复杂性。

Abundant use of RAM is not only inefficient, but many Electron applications require additional Electron specific code interfaces with NodeJS and is not necessarily the same as it’s corresponding web application. Electron also requires additional complexity by having to manage support for older versions and software update pipelines.

[桌面电脑输入]。想象一个世界，您可以利用电子应用程序的外观和感觉，而无需冗长的安装，软件更新，所有这些都在运送单个Web应用程序时。Chrome似乎正在为桌面电路板设置此行为的标准。当您访问Chrome中的网页并且该应用程序符合所有条件时，开发人员可以提示用户在其桌面上安装该应用程序。提示符如下所示：
**\[Desktop PWAs enter\]**. Imagine a world where you could leverage the look and feel of an Electron Application, without requiring lengthy installs, software updates, all while shipping a single web application. Chrome seems to be setting the standard for this behavior in Desktop PWAs. When you visit a webpage in Chrome and the application meets all the [criteria](https://developers.google.com/web/progressive-web-apps/checklist), the developer has the ability to prompt the user to install the application on their desktop. The prompt looks something like this:

![](/images/JS_Weekly/goodbye-electron-hello-desktop-pwas/1_a-GzDoKCwAirgklgMIWp0Q.png)

只有在浏览器触发窗口事件“beforeinstallprompt”后，开发人员才能显示此提示。一旦安装，这个应用程序将通常成为用户桌面上的应用程序图标。
This prompt can be shown at the developers discretion only after the window event _‘beforeinstallprompt’_ is fired by the browser. Once installed, this application will usually become an application icon on users desktop.

![](/images/JS_Weekly/goodbye-electron-hello-desktop-pwas/1_jsRf3VEYci3AHPHBELqM5Q.png)

启动这个新安装的应用程序后，您将获得一个新的最小浏览器窗口，其外观和感觉就像一个真正的桌面应用程序。

![](/images/JS_Weekly/goodbye-electron-hello-desktop-pwas/1_xb103Yfo58QRyISMB44bBg.png)

### 我的游乐场示例 My Playground Example

[**Desktop PWAs**  
Proof of Concept when implementing Desktop PWAs www.desktop-pwas.com](https://www.desktop-pwas.com/ "https://www.desktop-pwas.com/")[](https://www.desktop-pwas.com/)

[**supercycle91/desktop-pwas**  
_Experimenting with Desktop PWAs. Contribute to supercycle91/desktop-pwas development by creating an account on GitHub._ github.com](https://github.com/supercycle91/desktop-pwas "https://github.com/supercycle91/desktop-pwas")[](https://github.com/supercycle91/desktop-pwas)
上面的快照是我汇总的一个简单的概念证明，向您展示如何开始使用Desktop PWA。要安装它，请确保已启用桌面PWA功能标志，并且您在我的站点上处于活动状态至少30秒。请务必注意，测试Desktop PWA时有多个要求。如果您想要下载我的存储库，请按照桌面PWA清单进行操作，但这里有一个快速摘要：
The snapshots above are a simple proof of concept I have put together to show you how you can get started with Desktop PWAs. To install it, be sure you enabled the desktop PWA feature flag and you are active on my site for at least 30 seconds. It is important to note that there are multiple [requirements](https://developers.google.com/web/progressive-web-apps/checklist) when testing Desktop PWAs. If you would like to pull down my repository, please follow the [Desktop PWA checklist](https://developers.google.com/web/progressive-web-apps/checklist) but here is a quick summary:

必须安装一个具有至少一个提取处理程序的Service Worker


*   **必须通过HTTPS提供**
*   **Must have an installed Service Worker with at least one fetch handler**
*   **必须提供有效的manifest.json**
*   **页面必须是响应式的**

### 其他例子
如果您当前使用的是MacOS，许多应用程序并未假设您已启用桌面PWA功能标记，因此不会提示您进行安装。通过在Chrome中启用一些正确的标记，以及在Chrome Devtools控制台中进行精明的编码，您可以使用现有的PWA并让它们立即安装。以下是我能够在桌面上安装的一些现有PWA。
If you are currently on MacOS, many applications are not assuming you have the Desktop PWA feature flag enabled and thus do not prompt you to install. With some correct flag enabling in Chrome, and savvy coding in Chrome Devtools Console, you can take existing PWAs and have them prompt to install. Here are some existing PWAs I have been able to install on my desktop.

#### 星巴克
星巴克在创建一个伟大的PWA方面投入了大量资金。虽然不是我常用的东西，但桌面安装的版本确实令人印象深刻。
Starbucks have invested heavily in creating a great PWA. Although not something I frequent, the Desktop installed version is really impressive.

![](/images/JS_Weekly/goodbye-electron-hello-desktop-pwas/1_qfN90_BOlI6lrTQy5Xr3Aw.png)

#### Google Maps
这个应用真的很酷。非常轻巧且高效。反应迅速。获取Google地图的诀窍是强行加载移动网站。
This app is really cool. Very lightweight and gets the job done. Responsive and quick. The trick to getting Google maps is to force load the Mobile site.

![](/images/JS_Weekly/goodbye-electron-hello-desktop-pwas/1_aaE0qBqRSHNc3-CTkIQD3A.png)

#### Twitter
Twitter确实为手机上的Twitter PWA设置了很好的基线，安装的桌面版本对它们的实现非常有益。
Twitter has really been setting great baselines for the Twitter PWA on mobile, the installed desktop version is a great compliment to their implementation.

![](/images/JS_Weekly/goodbye-electron-hello-desktop-pwas/1_ag_iHuF5KM1AV1XPOWubzg.png)

### 后记
随着桌面电路板的所有嗡嗡声，理解标准仍处于初期阶段是明智之举。正在发展和变化，包括：
With all the buzz around Desktop PWAs, it is wise to understand the standards are still in it’s infancy. Developments and changes are being made including things such as:
深度链接：打开Chrome中的链接以触发已安装的应用程序而不是Chrome。
**Deep Linking:** Opening links in Chrome to trigger the installed application instead of in Chrome.
图标徽章：类似于通过应用程序图标标记的移动应用程序通知。
**Icon badging:** Similar to a mobile application notification badging over the application icon.
键盘快捷键：能够在已安装的应用程序中侦听使用本机键盘快捷键。
**Keyboard Shortcuts:** Ability to listen to use native keyboard shortcuts inside the installed application.
一旦成熟，我看到许多今天的Electron应用程序找到了台式电路板的重要竞争对手。
Once maturation, I see many of today’s Electron applications finding a serious competitor with Desktop PWAs.

### 资源 Resources

[**supercycle91/desktop-pwas**  
_Experimenting with Desktop PWAs. Contribute to supercycle91/desktop-pwas development by creating an account on GitHub._ github.com](https://github.com/supercycle91/desktop-pwas "https://github.com/supercycle91/desktop-pwas")[](https://github.com/supercycle91/desktop-pwas)

[**Progressive Web Apps | Web | Google Developers**  
_Progressive Web Apps are now supported on the desktop!_ developers.google.com](https://developers.google.com/web/progressive-web-apps "https://developers.google.com/web/progressive-web-apps")[](https://developers.google.com/web/progressive-web-apps)

[**Desktop Progressive Web Apps | Web | Google Developers**  
_Progressive Web Apps work on the desktop, including Chrome OS, Mac, Linux, and Windows._ developers.google.com](https://developers.google.com/web/progressive-web-apps/desktop "https://developers.google.com/web/progressive-web-apps/desktop")[](https://developers.google.com/web/progressive-web-apps/desktop)

[**Progressive Web App Checklist | Web | Google Developers**  
_A checklist which breaks down all the things we think it takes to be a baseline progressive web app, and how to take it…_ developers.google.com](https://developers.google.com/web/progressive-web-apps/checklist "https://developers.google.com/web/progressive-web-apps/checklist")[](https://developers.google.com/web/progressive-web-apps/checklist)

[**Lighthouse | Tools for Web Developers | Google Developers**  
_Learn how to set up Lighthouse to audit your web apps._ developers.google.com](https://developers.google.com/web/tools/lighthouse/ "https://developers.google.com/web/tools/lighthouse/")[](https://developers.google.com/web/tools/lighthouse/)

## 资源
* 原文 https://medium.com/dailyjs/goodbye-electron-hello-desktop-pwas-f316b8f39882
* PWA https://developers.google.com/web/progressive-web-apps/
