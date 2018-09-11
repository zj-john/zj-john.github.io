---
title: geth attach命令
categories:
  - notes
tags:
  - geth
  - attach
  - console
  - ethereum
toc: true
date: 2018-04-03 18:49:02
---

geth attach命令


<!-- more -->

# 描述

该命令允许在正在运行的geth节点上打开另一个控制台。



# 基本用法
```
USAGE:
   geth attach [command options] [arguments...]

ETHEREUM OPTIONS:
 --datadir "/root/.ethereum"  数据库和密钥库的数据目录

API AND CONSOLE OPTIONS:
 --jspath loadScript  设置加载JS脚本的根路径(默认是"."，为console所在的当前目录)
 --exec value         执行JavaScript语句
 --preload value      把以逗号分隔的JavaScript文件列表预加载到控制台中
```

# 示例
## 新开一个geth控制台
这个命令主要是另开一个geth控制台。

例如我开了一个geth：
```
> ./start.sh
INFO [04-09|05:07:58] Maximum peer count                       ETH=25 LES=0 total=25
INFO [04-09|05:07:58] Starting peer-to-peer node               instance=Geth/v1.8.2-stable-b8b9f7f4/linux-amd64/go1.9.4
INFO [04-09|05:07:58] Allocated cache and file handles         database=/root/Documents/ethereum/data/geth/chaindata cache=768 handles=512
INFO [04-09|05:07:59] Initialised chain configuration          config="{ChainID: 1106 Homestead: 0 DAO: <nil> DAOSupport: false EIP150: <nil> EIP155: 0 EIP158: 0 Byzantium: <nil> Constantinople: <nil> Engine: unknown}"
INFO [04-09|05:07:59] Disk storage enabled for ethash caches   dir=/root/Documents/ethereum/data/geth/ethash count=3
INFO [04-09|05:07:59] Disk storage enabled for ethash DAGs     dir=/root/.ethash                             count=2
INFO [04-09|05:07:59] Initialising Ethereum protocol           versions="[63 62]" network=100000
INFO [04-09|05:07:59] Loaded most recent local header          number=14 hash=86cf03…65a6b9 td=1967872
INFO [04-09|05:07:59] Loaded most recent local full block      number=14 hash=86cf03…65a6b9 td=1967872
INFO [04-09|05:07:59] Loaded most recent local fast block      number=14 hash=86cf03…65a6b9 td=1967872
INFO [04-09|05:07:59] Loaded local transaction journal         transactions=0 dropped=0
INFO [04-09|05:07:59] Regenerated local transaction journal    transactions=0 accounts=0
WARN [04-09|05:07:59] Blockchain not empty, fast sync disabled
INFO [04-09|05:07:59] Starting P2P networking
INFO [04-09|05:08:01] UDP listener up                          self=enode://09200d47050993bac053ec038ab90046d8ce5e6c82005f4f1c2a34704f523d7545444d6f3c921f40545fe96b6b56b0b12019cd444ec072493f6db9f77025e590@[::]:30303
INFO [04-09|05:08:01] HTTP endpoint opened                     url=http://127.0.0.1:8545 cors=* vhosts=localhost
INFO [04-09|05:08:01] RLPx listener up                         self=enode://09200d47050993bac053ec038ab90046d8ce5e6c82005f4f1c2a34704f523d7545444d6f3c921f40545fe96b6b56b0b12019cd444ec072493f6db9f77025e590@[::]:30303
INFO [04-09|05:08:01] IPC endpoint opened                      url=/root/Documents/ethereum/data/geth.ipc
Welcome to the Geth JavaScript console!

instance: Geth/v1.8.2-stable-b8b9f7f4/linux-amd64/go1.9.4
INFO [04-09|05:08:01] Etherbase automatically configured       address=0x93a44e1E0aCeb6a68ED39018d48A359b0beB8Eb5
coinbase: 0x93a44e1e0aceb6a68ed39018d48a359b0beb8eb5
at block: 14 (Fri, 30 Mar 2018 01:22:07 PDT)
 datadir: /root/Documents/ethereum/data
 modules: admin:1.0 debug:1.0 eth:1.0 miner:1.0 net:1.0 personal:1.0 rpc:1.0 txpool:1.0 web3:1.0

```
start.sh的内容
```shell
#!/bin/bash
geth --rpc \
     --rpccorsdomain "*" \
     --datadir "/root/Documents/ethereum/data" \
     --networkid 100000 \
     console
```

注意以下内容，后面新开geth控制台时会遇到
```
INFO [04-09|05:08:01] HTTP endpoint opened                     url=http://127.0.0.1:8545 cors=* vhosts=localhost
...
INFO [04-09|05:08:01] IPC endpoint opened                      url=/root/Documents/ethereum/data/geth.ipc
```


如果这个时候我再开一个终端，执行同样的命令,会报以下错误：
```
root@ubuntu:~/Documents# ./start.sh
INFO [04-09|05:09:29] Maximum peer count                       ETH=25 LES=0 total=25
Fatal: Error starting protocol stack: datadir already used by another process

```

这个时候我们可以执行以下代码，就会新打开一个geth控制台
```
> geth attach --datadir "/root/Documents/ethereum/data/"
Welcome to the Geth JavaScript console!

instance: Geth/v1.8.2-stable-b8b9f7f4/linux-amd64/go1.9.4
coinbase: 0x93a44e1e0aceb6a68ed39018d48a359b0beb8eb5
at block: 14 (Fri, 30 Mar 2018 01:22:07 PDT)
 datadir: /root/Documents/ethereum/data
 modules: admin:1.0 debug:1.0 eth:1.0 miner:1.0 net:1.0 personal:1.0 rpc:1.0 txpool:1.0 web3:1.0

>

```

还可以使用以下两个命令，也是同样的效果,下面的地址来自于第一个geth启动时的数据。
```shell
geth attach ipc://root/Documents/ethereum/data/geth.ipc
//or
geth attach http://127.0.0.1:8545
```

## 执行JS
我们在这个目录下新建一个test.js文件（/root/Documents/test.js），内容如下：
```
var aForTest = 5;
var bForTest = aForTest + 1;
console.log(aForTest, bForTest);
console.log(eth.accounts);
```

执行以下命令，得到结果5, 6和accounts的值，说明既可以执行原生JS命令，又可以结合geth带的API。
```
> geth attach http://127.0.0.1:8545 --jspath "/root/Documents" --exec "loadScript('test.js')"
5 6
0x93a44e1e0aceb6a68ed39018d48a359b0beb8eb5,0x68512ebacb81a3274933de1ac02a787927ef76d5,0x0ed90cf4d6a37cbcf7c4b9461f151c6f1fe2284b,0xf96a0654d887881cd8cdd5c4320125ae90515a0d
true

```
