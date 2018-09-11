---
title: VPS搭VPN
categories:
  - notes
tags:
  - VPS
  - VPN
toc: true
date: 2018-06-30 11:20:26
---

由于访问外部网站的需求，需要用到VPN，这里记录下通过VPS搭给自己使用的VPN的方法。

## VPS
VPS（虚拟专用服务器）选择的是[搬瓦工](https://bandwagonhost.com)。  
![](/images/banwagon.png)

当前最便宜的是10G VPS（含10GB SSD，512MB RAM，500GB 流量，1x Intel Xeon CPU，支持多设备），一年是19.99刀，折合下来一个月10几块软妹币，还是相当划算的。

购买之后，就可以看到你的虚拟机的IP和端口，然后用户密码会通过你注册的邮箱发给你。  
此时,你就可以使用xshell等ssh工具远程到主机上操作了。

> bandwagon的后台提供资源管理，重装系统，远程shell等基础功能，还有一键OpenVPN等扩展功能。这些扩展功能是变动的，有些VPS提供一键shadowsocks的功能，最新买的看已经没有这一项了。

![](/images/banwagon_admin.png)


## [OpenVPN](https://openvpn.net/)
通过banwagon的一键OpenVPN功能，可以一键安装OpenVPN。  
OpenVPN支持在windows、mac、Android、IOS上安装，在官网有各个平台需要的下载包。安装很简单，不在赘述。  

MAC的安装配置可以参考[文档](https://www.jianshu.com/p/a5fd8dc95ad4)

## [ShadowSocks](https://shadowsocks.org/en/index.html)
由于OpenVPN无法精细化管理（比如VPN只提供给浏览器、NPM使用，而不是全开），我们使用ShadowSocks来帮助实现这一需求。Shadowsocks可以把VPN映射到本地地址和端口，来提供给浏览器、GIT、NPM等使用。

### 安装
server端
```shell
pip install shadowsocks
```

client端（含手机端）：  
https://shadowsocks.org/en/download/clients.html

### 配置
新建文件：/etc/shadowsocks.json
```js
{
    "server":"my_server_ip", //your hostname or server IP (IPv4/IPv6).
    "server_port":8388, //server port number.
    "local_port":1080, //local port number.
    "password":"barfoo!", //a password used to encrypt transfer.
    "timeout":600, //connections timeout in seconds.
    "method":"aes-256-cfb" //encryption method
}
```

### 命令
```shell
#启动
ssserver -c /etc/shadowsocks.json -d start
# 关闭
ssserver -c /etc/shadowsocks.json -d stop
# 重启
ssserver -c /etc/shadowsocks.json -d restart
```

## 参考
* 搬瓦工官网：https://bandwagonhost.com
* OpenVPN官网： https://openvpn.net/
* MAC安装OpenVPN：https://www.jianshu.com/p/a5fd8dc95ad4
* ShadowSocks官网: https://shadowsocks.org/en/index.html
* ShadowSocks配置： https://segmentfault.com/a/1190000010528542
