---
title: vagrant up 命令无反应
categories:
  - tips
tags:
  - vagrant
  - powershell
toc: true
date: 2017-12-07 10:18:30
---
> 问题：vagrant up 命令无反应

由于开源项目的需要，要在windows下设置linux开发环境，同事介绍了[vagrant](https://www.vagrantup.com/)这款软件。   
安装vagrant的过程中，设置好box，init后，使用vagrant up命令启动时，无报错，无反馈。

<!-- more -->

# 解决方法
使用debug参数查看up命令的详细过程
```
vagrant up --debug
```
发现卡在如下输出上：
```
INFO subprocess: Starting process: ["C:\windows\System32\WindowsPowerShell\v1.0\/powershell.EXE", "-NoLogo", "-NoProfile", "-NonInteractive", "-ExecutionPolicy", "Bypass", "-Command", "(new-object System.Security.Principal.WindowsPrincipal([System.Security.Principal.WindowsIdentity]::GetCurrent())).IsInRole([System.Security.Principal.WindowsBuiltInRole]::Administrator)"]
INFO subprocess: Command not in installer, restoring original environment...
DEBUG subprocess: Selecting on IO
DEBUG subprocess: stdout: False
```

看起来像是powershell的问题，在vargrant的issue上找了下，看大神们的解答是因为powershell版本的问题。  
使用以下命令查看，win7默认版本是powershell 2。通过补丁update版本到[5.0](https://www.microsoft.com/en-us/download/details.aspx?id=50395)。
```
$PSVersionTable.PSVersion
```

之后再vagrant up就可以成功了。


# 其它问题
目前已实现可以通过端口映射、ip映射的方式把node后端、react前端映射到win7自己的浏览器中，不过有一个问题是hot server无法映射到win7的浏览器上，每次更新后要手动刷新。
