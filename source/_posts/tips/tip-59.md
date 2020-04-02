---
title: 当升级到win10……
categories:
  - tips
tags:
  - win10
  - chrome
  - host
toc: false
date: 2020-04-01 20:45:16
---

   
服役多年的win7在一次重启后，蓝屏了……  
告别了win7，还来不及有迎接win10的喜悦，win10下的一些问题暴露了……

<!-- more -->



## where is hosts

安装win10后，原先driver下没有etc文件夹，也找不到熟悉的hosts文件了。

解决方法：   
找到cmd，选择用管理员帐户打开：输入以下命令
```
for /f %P in ('dir %windir%\WinSxS\hosts /b /s') do copy %P %windir%\System32\drivers\etc & echo %P & Notepad %P
```
当显示“已复制1个文件。”时即表示命令完成，同时将使用记事本打开 hosts 文件，此时 hosts 文件即恢复。



## chrome80跨域

安装了最新版本80后，使用原先72版本的方式跨域，发现未成功。

chrome72版本时：
```
"C:\Program Files (x86)\Google\Chrome\Application\chrome.exe" --disable-web-security --user-data-dir="c:\chrome_kuayu"
```

到了chrome80版本，虽然这样设置后，打开chrome显示熟悉的“不安全提示”，但是真正测试一个跨域应用时，发现并未成功，console中有如下warning提示：

```
A cookie associated with a cross-site resource at http://XXXXX.com/ was set without the `SameSite` attribute. A future release of Chrome will only deliver cookies with cross-site requests if they are set with `SameSite=None` and `Secure`. You can review cookies in developer tools under Application>Storage>Cookies and see more details at https://www.chromestatus.com/feature/5088147346030592 and https://www.chromestatus.com/feature/5633521622188032.
```

这里表述说，未来的版本将对cookie的安全加强，如果想要跨域，需要新增SameSite的tag标识才可以。  

但是这里说了是future，那现在应该只是试用了。

我们在chrome中输入
```
chrome://flags/
```
看到当前浏览器支持配置的各个属性。

![](/images/tips/chrome_flag.png)

找到SameSite by default cookies，把它disable掉。   
之后访问就可以继续跨域访问了。

## gulp2.7 && node12

最新安装的node12下，运行之前一个特别老的项目（gulp2.7运行），报错

```
ReferenceError: primordials is not defined
```

在stackoverflow中找到了答案，虽然上面说在gulp3.9和node12的搭配下会产生这个问题，但实际验证下来，应该是有多个gulp版本有这个问题。

看下来是由于graceful-fs这个包的版本导致的问题（具体未查）。

解决方法：

利用npm的钩子，在安装之前安装graceful-fs 4.2.3。
```
{
  // Your current package.json
  "scripts": {
    // Your current package.json scripts
    "preinstall": "npx npm-force-resolutions"
  },
  "resolutions": {
    "graceful-fs": "4.2.3"
  }
}
```

## 参考文档
1. https://github.com/google/google-api-javascript-client/issues/561
2. https://stackoverflow.com/questions/55921442/how-to-fix-referenceerror-primordials-is-not-defined-in-node

