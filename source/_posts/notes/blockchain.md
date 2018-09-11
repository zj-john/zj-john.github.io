---
title: blockchain 笔记
categories:
  - notes
tags:
  - blockchain
  - ethereum
toc: true
date: 2018-03-28 14:20:51
---

# blockchain 笔记

最近在学习区块链，先做些记录，后续再整理

<!-- more -->

## 书籍教程
* 区块链技术指南: [地址](https://www.gitbook.com/book/yeasy/blockchain_guide)
* 论文【以太坊：一种安全去中心化的通用交易账本】：[中文地址](http://download.cxyym.com/blockchain/ethereum_yellowpaper_cn.pdf)
* Node.js开发加密货币： [地址](http://bitcoin-on-nodejs.ebookchain.org/)
* 知乎贴： [地址](https://www.zhihu.com/question/51047975)
* 以太坊中文教程： [地址](https://ethfans.org/wikis/Home)


## 比特币
[当前交易信息](https://blockchain.info/)  
查看最近的区块，区块大小、传输数据、交易笔数等信息。

[具体交易](https://blockchain.info/block/000000000000000000080b3983b691ef1465839dc6286495c8d76d169629d6a9)  
某个区块上具体的交易信息、上一块的hash，本区块的hash（通过以上两个hash可以开始计算下一个块的hash地址了）、矿工、交易费等



## 技术实践
### 以太坊安装（Ubuntu版本）
[参考文档](https://yeasy.gitbooks.io/blockchain_guide/content/ethereum/install.html)

#### 安装 Go 环境
```shell
  curl -O https://storage.googleapis.com/golang/go1.5.1.linux-amd64.tar.gz
  tar -C /usr/local -xzf go1.5.1.linux-amd64.tar.gz
  mkdir -p ~/go; echo "export GOPATH=$HOME/go" >> ~/.bashrc
  echo "export PATH=$PATH:$HOME/go/bin:/usr/local/go/bin" >> ~/.bashrc
  source ~/.bashrc
```
安装之后，terminal中直接输入 go 来验证是否安装成功。


#### 安装 ethereum
```shell
  sudo apt-get install software-properties-common
  sudo add-apt-repository -y ppa:ethereum/ethereum
  sudo add-apt-repository -y ppa:ethereum/ethereum-dev
  sudo apt-get update
  sudo apt-get install ethereum
```
安装之后，terminal中直接输入 geth --help 来验证ethereum是否安装成功。


如果需要智能合约的话，还需要安装 solc 编译器(Solidity语言)
```shell
  sudo add-apt-repository ppa:ethereum/ethereum-qt
  sudo add-apt-repository ppa:ethereum/ethereum
  sudo apt-get update
  sudo apt-get install cpp-ethereum
```

### geth
* 修改储存区块数据的地址  
geth -datadir "/media/ethereum/"

* 同步区块数据  
geth

* 快速同步区块数据  
geth -fast

* 创建账户  
geth account new

>记好密码，唯一凭证

* 查看账户  
geth account list


## 产品
* 网易星球  
邀请码：ADQC7X   
上车码：https://zhuanlan.zhihu.com/p/33722443
