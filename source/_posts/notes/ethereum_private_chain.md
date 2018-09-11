---
title: 以太坊私链搭建
categories:
  - notes
tags:
  - private chain
  - ethereum
toc: true
date: 2018-03-29 12:23:24
---

以太坊私链搭建


<!-- more -->
# 安装

系统：Ubuntu

## 环境
### GO
下载go包，安装并加入环境变量
```shell
> curl -O https://storage.googleapis.com/golang/go1.5.1.linux-amd64.tar.gz
> tar -C /usr/local -xzf go1.5.1.linux-amd64.tar.gz
> mkdir -p ~/go; echo "export GOPATH=$HOME/go" >> ~/.bashrc
> echo "export PATH=$PATH:$HOME/go/bin:/usr/local/go/bin" >> ~/.bashrc
> source ~/.bashrc
```
成功标志：
```Shell
> go version
go version go1.5.1 linux/amd64
```

### Ethereum
```bash
> sudo apt-get install software-properties-common
> sudo add-apt-repository -y ppa:ethereum/ethereum
> sudo add-apt-repository -y ppa:ethereum/ethereum-dev
> sudo apt-get update
> sudo apt-get install ethereum
```
成功标志：
```shell
> geth version
Geth
Version: 1.8.2-stable
Git Commit: b8b9f7f4476a30a0aaf6077daade6ae77f969960
Architecture: amd64
Protocol Versions: [63 62]
Network Id: 1
Go Version: go1.9.4
Operating System: linux
GOPATH=/root/go
GOROOT=/usr/lib/go-1.9
```
参考文档：  
区块链技术指南: [地址](https://www.gitbook.com/book/yeasy/blockchain_guide)

## 初始化
### 新建项目目录
```shell
cd /root/Documents/
mkdir ethereum
cd ethereum
mkdir data
touch init.json
```
ethereum是项目目录，新建目录data用以存放databases和keystore,新建文件init.json用于初始化的配置。以上目录文件名称等可以自定。

### init配置
配置内容参考[官网](https://github.com/ethereum/go-ethereum#operating-a-private-network)
init.json内容如下:
```json
{
  "config": {
        "chainId": 1106,
        "homesteadBlock": 0,
        "eip155Block": 0,
        "eip158Block": 0
    },
  "alloc"      : {},
  "coinbase"   : "0x0000000000000000000000000000000000000000",
  "difficulty" : "0x20000",
  "extraData"  : "",
  "gasLimit"   : "0x2fefd8",
  "nonce"      : "0x0000000000000042",
  "mixhash"    : "0x0000000000000000000000000000000000000000000000000000000000000000",
  "parentHash" : "0x0000000000000000000000000000000000000000000000000000000000000000",
  "timestamp"  : "0x00"
}
```
如果需要初始化的时候添加账户，可以在alloc中注明
```json
"alloc": {
  "0x0000000000000000000000000000000000000001": {
    "balance": "111111111"
  },
  "0x0000000000000000000000000000000000000002": {
    "balance": "222222222"
  }
}
```
* mixhash:  
与nonce配合用于挖矿，由上一个区块的一部分生成的hash。注意他和nonce的设置需要满足以太坊的Yellow Paper，Block Header Validity（44）章节中所描述的条件

* nonce：  
nonce就是一个64位的随机数，用于挖矿，注意他和mixhash的设置需要满足以太坊的Yellow Paper，Block Header Validity（44）章节中所描述的条件

* difficulty:  
设置当前区块的难度，如果难度过大，cpu挖矿就困难。私有链设置的小一些。

* alloc：  
用于预设值账号已经账号的以太币数量。

* coinbase：  
 矿工的账号，随意填

* timestamp:  
创世块的时间戳

* parentHash：  
上一个区块的hash值，因为是创世块，所以这个值为0

* extraData：  
附加信息

* gasLimit：  
该值设置对gas的消耗总量限制，用来限制区块能包含的交易信息的总和。私有链填最大。

具体的key的含义参考官网[文档](https://github.com/ethereum/go-ethereum)即可。


> 使用中发现extraData不为空时，会报错
Fatal: invalid genesis file: json: cannot unmarshal hex string without 0x prefix into Go struct field Genesis.extraData of type hexutil.Bytes


> Attention: chainId不能为0，为0时，在后续转账是无法成功。报错
Error: insufficient funds for gas * price + value

> Attention: chainId 和 后续的networkid 保持一致，否则报错Error: insufficient funds for gas * price + value。 见[issue](https://github.com/ethereum/go-ethereum/issues/15983)。

### 创世操作
执行以下命令初始化系统
```shell
> pwd
/root/Documents/ethereum
> geth --datadir "/root/Documents/ethereum/data" init init.json
```
正常结果如下：
```
INFO [03-27|19:56:49] Maximum peer count                       ETH=25 LES=0 total=25
INFO [03-27|19:56:49] Allocated cache and file handles         database=/root/Documents/ethereum/data/geth/chaindata cache=16 handles=16
INFO [03-27|19:56:49] Writing custom genesis block
INFO [03-27|19:56:49] Persisted trie from memory database      nodes=0 size=0.00B time=38.164µs gcnodes=0 gcsize=0.00B gctime=0s livenodes=1 livesize=0.00B
INFO [03-27|19:56:49] Successfully wrote genesis state         database=chaindata                                    hash=5e1fc7…d790e0
INFO [03-27|19:56:49] Allocated cache and file handles         database=/root/Documents/ethereum/data/geth/lightchaindata cache=16 handles=16
INFO [03-27|19:56:49] Writing custom genesis block
INFO [03-27|19:56:49] Persisted trie from memory database      nodes=0 size=0.00B time=2.884µs  gcnodes=0 gcsize=0.00B gctime=0s livenodes=1 livesize=0.00B
INFO [03-27|19:56:49] Successfully wrote genesis state         database=lightchaindata
```

接着执行以下命令 启动运行
```
geth --rpc --rpccorsdomain "*" --datadir "/root/Documents/ethereum/data" --networkid 100000 console 2>>geth.log
```
参数的含义请参考[Command文档](https://github.com/ethereum/go-ethereum/wiki/Command-Line-Options).

|参数|	参数说明|
|--|--|
|datadir	| 设置当前区块链网络数据存放的位置|
|console	| 启动命令行模式，可以在geth里执行命令|
|nodiscover	| 关闭p2p网络的自动发现，不会被网上看到|
|networkid	| 网络标识，用于区分不同的网络，私有链取一个大于4的随意的值|
|identity| 区块链的标示，随便填写，用于标示目前网络的名字|
|init|指定创世块文件的位置，并创建初始块|
|rpc|启动rpc通信，可以进行智能合约的部署和调试|
|rpcapi|设置允许连接的rpc的客户端，一般为db，net，eth，web3|



正常结果返回Welcome to the Geth JavaScript console!

```
INFO [03-27|21:51:13] Maximum peer count                       ETH=25 LES=0 total=25
INFO [03-27|21:51:13] Starting peer-to-peer node               instance=Geth/v1.8.2-stable-b8b9f7f4/linux-amd64/go1.9.4
INFO [03-27|21:51:13] Allocated cache and file handles         database=/root/Documents/ethereum/data/geth/chaindata cache=768 handles=512
INFO [03-27|21:51:13] Initialised chain configuration          config="{ChainID: 0 Homestead: 0 DAO: <nil> DAOSupport: false EIP150: <nil> EIP155: 0 EIP158: 0 Byzantium: <nil> Constantinople: <nil> Engine: unknown}"
INFO [03-27|21:51:13] Disk storage enabled for ethash caches   dir=/root/Documents/ethereum/data/geth/ethash count=3
INFO [03-27|21:51:13] Disk storage enabled for ethash DAGs     dir=/root/.ethash                             count=2
INFO [03-27|21:51:13] Initialising Ethereum protocol           versions="[63 62]" network=100000
INFO [03-27|21:51:13] Loaded most recent local header          number=0 hash=5e1fc7…d790e0 td=131072
INFO [03-27|21:51:13] Loaded most recent local full block      number=0 hash=5e1fc7…d790e0 td=131072
INFO [03-27|21:51:13] Loaded most recent local fast block      number=0 hash=5e1fc7…d790e0 td=131072
INFO [03-27|21:51:13] Loaded local transaction journal         transactions=0 dropped=0
INFO [03-27|21:51:13] Regenerated local transaction journal    transactions=0 accounts=0
INFO [03-27|21:51:13] Starting P2P networking
INFO [03-27|21:51:16] UDP listener up                          self=enode://09200d47050993bac053ec038ab90046d8ce5e6c82005f4f1c2a34704f523d7545444d6f3c921f40545fe96b6b56b0b12019cd444ec072493f6db9f77025e590@[::]:30303
INFO [03-27|21:51:16] HTTP endpoint opened                     url=http://127.0.0.1:8545 cors=* vhosts=localhost
INFO [03-27|21:51:16] RLPx listener up                         self=enode://09200d47050993bac053ec038ab90046d8ce5e6c82005f4f1c2a34704f523d7545444d6f3c921f40545fe96b6b56b0b12019cd444ec072493f6db9f77025e590@[::]:30303
INFO [03-27|21:51:16] IPC endpoint opened                      url=/root/Documents/ethereum/data/geth.ipc
Welcome to the Geth JavaScript console!

instance: Geth/v1.8.2-stable-b8b9f7f4/linux-amd64/go1.9.4
 modules: admin:1.0 debug:1.0 eth:1.0 miner:1.0 net:1.0 personal:1.0 rpc:1.0 txpool:1.0 web3:1.0

>
```

操作到现在，私有链已经搭建好了，只是现在还没有账户，也没有交易，只有一个环境。

最终的ethereum目录结构如下：
```
.
├── data
│   ├── geth
│   │   ├── chaindata
│   │   │   ├── 000002.ldb
│   │   │   ├── 000005.ldb
│   │   │   ├── 000006.log
│   │   │   ├── CURRENT
│   │   │   ├── LOCK
│   │   │   ├── LOG
│   │   │   └── MANIFEST-000007
│   │   ├── lightchaindata
│   │   │   ├── 000001.log
│   │   │   ├── CURRENT
│   │   │   ├── LOCK
│   │   │   ├── LOG
│   │   │   └── MANIFEST-000000
│   │   ├── LOCK
│   │   ├── nodekey
│   │   ├── nodes
│   │   │   ├── 000011.log
│   │   │   ├── 000013.ldb
│   │   │   ├── CURRENT
│   │   │   ├── LOCK
│   │   │   ├── LOG
│   │   │   └── MANIFEST-000012
│   │   └── transactions.rlp
│   ├── geth.ipc
│   ├── history
│   └── keystore
└── init.json

```


## 挖矿

>写在前面的话：如果是像我一样跑在虚拟机上的童鞋，请先调高你的虚拟机配置，初始的Ubuntu我的是1C1G，通过5h的挖矿探索，发现调高配置到4C2G，分分钟挖到了。血泪史。

接着上面的geth命令行，执行以下命令查询账户和新建账户。listAccounts为[],表示当前没有账户；newAccount()，输入密码就会新建一个账户，这时候再listAccounts就会显示刚才创建的用户。
```
> personal.listAccounts
[]
> personal.newAccount()
Passphrase:
Repeat passphrase:
"0x93a44e1e0aceb6a68ed39018d48a359b0beb8eb5"
> personal.listAccounts
["0x93a44e1e0aceb6a68ed39018d48a359b0beb8eb5"]
```
> personal.listAccounts 等同于eth.accounts，说明有很多包的方法有重复

geth的命令行支持的操作参见[文档](https://ethereum.gitbooks.io/frontier-guide/content/jsre.html#personallistaccounts)

现在有了第一个账户，可以开始挖矿了。

geth命令行执行以下命令，查当前账户的钱包。当前为0以太币
```
> acc0 = eth.accounts[0]
> eth.getBalance(acc0)
0
```

接着输入
```
> miner.start()
true
```
语法是：miner.start(threadCount),返回true表示开始挖矿

一次挖矿完整的记录如下
```
INFO [03-27|23:31:13] Generating DAG in progress               epoch=1 percentage=0  elapsed=4m15.013s
INFO [03-27|23:35:20] Generating DAG in progress               epoch=1 percentage=1  elapsed=8m22.403s
INFO [03-27|23:40:52] Generating DAG in progress               epoch=1 percentage=2  elapsed=13m54.069s
INFO [03-27|23:45:11] Generating DAG in progress               epoch=1 percentage=3  elapsed=18m12.980s
INFO [03-27|23:49:05] Generating DAG in progress               epoch=1 percentage=4  elapsed=22m7.204s
INFO [03-27|23:51:19] Regenerated local transaction journal    transactions=0 accounts=0
INFO [03-27|23:53:33] Generating DAG in progress               epoch=1 percentage=5  elapsed=26m34.627s
INFO [03-27|23:57:48] Generating DAG in progress               epoch=1 percentage=6  elapsed=30m49.961s
INFO [03-28|00:02:21] Generating DAG in progress               epoch=1 percentage=7  elapsed=35m22.462s
INFO [03-28|00:06:30] Generating DAG in progress               epoch=1 percentage=8  elapsed=39m31.955s
INFO [03-28|00:10:38] Generating DAG in progress               epoch=1 percentage=9  elapsed=43m39.886s
INFO [03-28|00:14:44] Generating DAG in progress               epoch=1 percentage=10 elapsed=47m46.109s
INFO [03-28|00:18:53] Generating DAG in progress               epoch=1 percentage=11 elapsed=51m55.415s
INFO [03-28|00:23:16] Generating DAG in progress               epoch=1 percentage=12 elapsed=56m17.590s
INFO [03-28|00:27:26] Generating DAG in progress               epoch=1 percentage=13 elapsed=1h0m27.989s
INFO [03-28|00:31:27] Generating DAG in progress               epoch=1 percentage=14 elapsed=1h4m28.462s
INFO [03-28|00:35:21] Generating DAG in progress               epoch=1 percentage=15 elapsed=1h8m22.313s
INFO [03-28|00:39:20] Generating DAG in progress               epoch=1 percentage=16 elapsed=1h12m21.312s
INFO [03-28|00:43:29] Generating DAG in progress               epoch=1 percentage=17 elapsed=1h16m30.009s
INFO [03-28|00:47:37] Generating DAG in progress               epoch=1 percentage=18 elapsed=1h20m38.194s
INFO [03-28|00:51:16] Regenerated local transaction journal    transactions=0 accounts=0
INFO [03-28|00:51:33] Generating DAG in progress               epoch=1 percentage=19 elapsed=1h24m34.492s
INFO [03-28|00:55:25] Generating DAG in progress               epoch=1 percentage=20 elapsed=1h28m26.685s
INFO [03-28|00:59:04] Generating DAG in progress               epoch=1 percentage=21 elapsed=1h32m4.969s
INFO [03-28|01:03:10] Generating DAG in progress               epoch=1 percentage=22 elapsed=1h36m11.437s
INFO [03-28|01:07:06] Generating DAG in progress               epoch=1 percentage=23 elapsed=1h40m7.738s
INFO [03-28|01:11:08] Generating DAG in progress               epoch=1 percentage=24 elapsed=1h44m9.167s
INFO [03-28|01:15:44] Generating DAG in progress               epoch=1 percentage=25 elapsed=1h48m45.854s
INFO [03-28|01:20:39] Generating DAG in progress               epoch=1 percentage=26 elapsed=1h53m40.293s
INFO [03-28|01:24:36] Generating DAG in progress               epoch=1 percentage=27 elapsed=1h57m37.955s
INFO [03-28|01:28:12] Generating DAG in progress               epoch=1 percentage=28 elapsed=2h1m13.753s
INFO [03-28|01:31:58] Generating DAG in progress               epoch=1 percentage=29 elapsed=2h4m59.767s
INFO [03-28|01:35:39] Generating DAG in progress               epoch=1 percentage=30 elapsed=2h8m40.917s
INFO [03-28|01:39:22] Generating DAG in progress               epoch=1 percentage=31 elapsed=2h12m23.719s
INFO [03-28|01:43:03] Generating DAG in progress               epoch=1 percentage=32 elapsed=2h16m4.180s
INFO [03-28|01:46:56] Generating DAG in progress               epoch=1 percentage=33 elapsed=2h19m57.810s
INFO [03-28|01:50:24] Generating DAG in progress               epoch=1 percentage=34 elapsed=2h23m26.296s
INFO [03-28|01:51:19] Regenerated local transaction journal    transactions=0 accounts=0
INFO [03-28|01:54:03] Generating DAG in progress               epoch=1 percentage=35 elapsed=2h27m4.613s
INFO [03-28|01:57:47] Generating DAG in progress               epoch=1 percentage=36 elapsed=2h30m49.006s
INFO [03-28|02:01:29] Generating DAG in progress               epoch=1 percentage=37 elapsed=2h34m31.396s
INFO [03-28|02:05:15] Generating DAG in progress               epoch=1 percentage=38 elapsed=2h38m16.808s
INFO [03-28|02:08:59] Generating DAG in progress               epoch=1 percentage=39 elapsed=2h42m1.220s
INFO [03-28|02:13:00] Generating DAG in progress               epoch=1 percentage=40 elapsed=2h46m1.605s
INFO [03-28|02:16:40] Generating DAG in progress               epoch=1 percentage=41 elapsed=2h49m42.243s
INFO [03-28|02:20:20] Generating DAG in progress               epoch=1 percentage=42 elapsed=2h53m22.062s
INFO [03-28|02:24:09] Generating DAG in progress               epoch=1 percentage=43 elapsed=2h57m11.394s
INFO [03-28|02:27:50] Generating DAG in progress               epoch=1 percentage=44 elapsed=3h0m52.296s
INFO [03-28|02:31:27] Generating DAG in progress               epoch=1 percentage=45 elapsed=3h4m28.628s
INFO [03-28|02:35:18] Generating DAG in progress               epoch=1 percentage=46 elapsed=3h8m20.178s
INFO [03-28|02:38:58] Generating DAG in progress               epoch=1 percentage=47 elapsed=3h11m59.768s
INFO [03-28|02:42:38] Generating DAG in progress               epoch=1 percentage=48 elapsed=3h15m39.901s
INFO [03-28|02:46:29] Generating DAG in progress               epoch=1 percentage=49 elapsed=3h19m31.220s
INFO [03-28|02:50:03] Generating DAG in progress               epoch=1 percentage=50 elapsed=3h23m5.428s
INFO [03-28|02:51:17] Regenerated local transaction journal    transactions=0 accounts=0
INFO [03-28|02:53:44] Generating DAG in progress               epoch=1 percentage=51 elapsed=3h26m46.427s
INFO [03-28|02:57:25] Generating DAG in progress               epoch=1 percentage=52 elapsed=3h30m26.974s
INFO [03-28|03:00:55] Generating DAG in progress               epoch=1 percentage=53 elapsed=3h33m57.345s
INFO [03-28|03:04:34] Generating DAG in progress               epoch=1 percentage=54 elapsed=3h37m36.322s
INFO [03-28|03:08:16] Generating DAG in progress               epoch=1 percentage=55 elapsed=3h41m17.787s
INFO [03-28|03:11:52] Generating DAG in progress               epoch=1 percentage=56 elapsed=3h44m53.677s
INFO [03-28|03:15:50] Generating DAG in progress               epoch=1 percentage=57 elapsed=3h48m51.747s
INFO [03-28|03:19:30] Generating DAG in progress               epoch=1 percentage=58 elapsed=3h52m32.327s
INFO [03-28|03:23:07] Generating DAG in progress               epoch=1 percentage=59 elapsed=3h56m9.105s
INFO [03-28|03:26:49] Generating DAG in progress               epoch=1 percentage=60 elapsed=3h59m50.899s
INFO [03-28|03:30:28] Generating DAG in progress               epoch=1 percentage=61 elapsed=4h3m29.553s
INFO [03-28|03:33:55] Generating DAG in progress               epoch=1 percentage=62 elapsed=4h6m57.427s
INFO [03-28|03:37:30] Generating DAG in progress               epoch=1 percentage=63 elapsed=4h10m32.180s
INFO [03-28|03:41:04] Generating DAG in progress               epoch=1 percentage=64 elapsed=4h14m6.429s
INFO [03-28|04:06:03] Generating DAG in progress               epoch=1 percentage=65 elapsed=4h18m11.501s
INFO [03-28|04:09:47] Generating DAG in progress               epoch=1 percentage=66 elapsed=4h21m55.415s
INFO [03-28|04:12:08] Regenerated local transaction journal    transactions=0 accounts=0
INFO [03-28|04:13:36] Generating DAG in progress               epoch=1 percentage=67 elapsed=4h25m44.487s
INFO [03-28|04:17:11] Generating DAG in progress               epoch=1 percentage=68 elapsed=4h29m20.175s
INFO [03-28|04:21:02] Generating DAG in progress               epoch=1 percentage=69 elapsed=4h33m10.483s
INFO [03-28|04:24:45] Generating DAG in progress               epoch=1 percentage=70 elapsed=4h36m53.288s
INFO [03-28|04:28:21] Generating DAG in progress               epoch=1 percentage=71 elapsed=4h40m29.837s
INFO [03-28|04:32:00] Generating DAG in progress               epoch=1 percentage=72 elapsed=4h44m8.371s
INFO [03-28|04:35:33] Generating DAG in progress               epoch=1 percentage=73 elapsed=4h47m41.299s
INFO [03-28|04:39:09] Generating DAG in progress               epoch=1 percentage=74 elapsed=4h51m17.830s
INFO [03-28|04:42:37] Generating DAG in progress               epoch=1 percentage=75 elapsed=4h54m46.152s
INFO [03-28|04:46:18] Generating DAG in progress               epoch=1 percentage=76 elapsed=4h58m26.446s
INFO [03-28|04:50:00] Generating DAG in progress               epoch=1 percentage=77 elapsed=5h2m8.264s
INFO [03-28|04:53:42] Generating DAG in progress               epoch=1 percentage=78 elapsed=5h5m50.968s
INFO [03-28|04:57:31] Generating DAG in progress               epoch=1 percentage=79 elapsed=5h9m38.710s
INFO [03-28|05:01:14] Generating DAG in progress               epoch=1 percentage=80 elapsed=5h13m22.502s
INFO [03-28|05:05:14] Generating DAG in progress               epoch=1 percentage=81 elapsed=5h17m22.433s
INFO [03-28|05:09:26] Generating DAG in progress               epoch=1 percentage=82 elapsed=5h21m34.496s
INFO [03-28|05:12:10] Regenerated local transaction journal    transactions=0 accounts=0
INFO [03-28|05:13:21] Generating DAG in progress               epoch=1 percentage=83 elapsed=5h25m30.173s
INFO [03-28|05:17:17] Generating DAG in progress               epoch=1 percentage=84 elapsed=5h29m10.749s
INFO [03-28|05:18:19] Generating DAG in progress               epoch=1 percentage=85 elapsed=5h30m12.264s
INFO [03-28|05:18:35] Generating DAG in progress               epoch=1 percentage=86 elapsed=5h30m29.213s
INFO [03-28|05:18:47] Successfully sealed new block            number=1 hash=ee7849...d9fa2
INFO [03-28|05:18:47] mined potential block                  number=1 hash=ee7849...d9fa2
INFO [03-28|05:18:47] Commit new mining work                   number=2 txs=0 uncles=0 elapsed=80.389ms
INFO [03-28|05:18:49] Generating DAG in progress               epoch=1 percentage=87 elapsed=5h30m42.735s
INFO [03-28|05:18:58] Successfully sealed new block            number=2 hash=71a6fe...8f13c
INFO [03-28|05:18:58] mined potential block                  number=2 hash=71a6fe...8f13c
INFO [03-28|05:18:58] Commit new mining work                   number=3 txs=0 uncles=0 elapsed=92.767s
INFO [03-28|05:19:02] Generating DAG in progress               epoch=1 percentage=88 elapsed=5h30m55.718s
INFO [03-28|05:19:15] Generating DAG in progress               epoch=1 percentage=89 elapsed=5h31m8.583s
INFO [03-28|05:19:21] Successfully sealed new block            number=3 hash=12e44e...743f9
INFO [03-28|05:19:21] mined potential block                  number=3 hash=12e44e...743f9
INFO [03-28|05:19:21] Commit new mining work                   number=4 txs=0 uncles=0 elapsed=110.894μs
INFO [03-28|05:19:25] Successfully sealed new block            number=4 hash=0cdb39...ba20b
INFO [03-28|05:19:25] mined potential block                  number=4 hash=0cdb39...ba20b
INFO [03-28|05:19:25] Commit new mining work                   number=5 txs=0 uncles=0 elapsed=135.77μs
INFO [03-28|05:19:28] Generating DAG in progress               epoch=1 percentage=90 elapsed=5h31m21.687s
INFO [03-28|05:19:33] Successfully sealed new block            number=5 hash=c90c64...91627
INFO [03-28|05:19:33] mined potential block                  number=5 hash=c90c64...91627
INFO [03-28|05:19:33] Commit new mining work                   number=6 txs=0 uncles=0 elapsed=175.941μs
INFO [03-28|05:19:34] Successfully sealed new block            number=6 hash=bb6175...89beb
INFO [03-28|05:19:34] block reached canonical chain          number=1 hash=ee7849...d9fa2
INFO [03-28|05:19:34] mined potential block                  number=6 hash=bb6175...89beb
INFO [03-28|05:19:34] Commit new mining work                   number=7 txs=0 uncles=0 elapsed=133.76μs
INFO [03-28|05:19:40] Generating DAG in progress               epoch=1 percentage=91 elapsed=5h31m34.113s
INFO [03-28|05:19:42] Successfully sealed new block            number=7 hash=047dee...55436
INFO [03-28|05:19:42] block reached canonical chain          number=2 hash=71a6fe...8f13c
INFO [03-28|05:19:42] mined potential block                  number=7 hash=047dee...55436
INFO [03-28|05:19:42] Commit new mining work                   number=8 txs=0 uncles=0 elapsed=95.689μs
> mINFO [03-28|05:19:46] Successfully sealed new block            number=8 hash=19e109...3fbde
INFO [03-28|05:19:46] block reached canonical chain          number=3 hash=12e44e...743f9
INFO [03-28|05:19:46] mined potential block                  number=8 hash=19e109...3fbde
INFO [03-28|05:19:46] Commit new mining work                   number=9 txs=0 uncles=0 elapsed=115.824μs
> miINFO [03-28|05:19:47] Successfully sealed new block            number=9 hash=4538a0...aa22e
INFO [03-28|05:19:47] block reached canonical chain          number=4 hash=0cdb39...ba20b
INFO [03-28|05:19:47] mined potential block                  number=9 hash=4538a0...aa22e
INFO [03-28|05:19:47] Commit new mining work                   number=10 txs=0 uncles=0 elapsed=109.851μs
> miner.stop()
INFO [03-28|05:19:51] Successfully sealed new block            number=10 hash=7e83b8...1f8f7
INFO [03-28|05:19:51] block reached canonical chain          number=5  hash=c90c64...91627
INFO [03-28|05:19:51] mined potential block                  number=10 hash=7e83b8...1f8f7
INFO [03-28|05:19:51] Commit new mining work                   number=11 txs=0 uncles=0 elapsed=181.645μs
INFO [03-28|05:19:52] Successfully sealed new block            number=11 hash=5c5aab...9d07d
INFO [03-28|05:19:52] block reached canonical chain          number=6  hash=bb6175...89beb
INFO [03-28|05:19:52] mined potential block                  number=11 hash=5c5aab...9d07d
INFO [03-28|05:19:52] Commit new mining work                   number=12 txs=0 uncles=0 elapsed=223.353μs
true
> INFO [03-28|05:19:53] Generating DAG in progress               epoch=1 percentage=92 elapsed=5h31m46.365s
INFO [03-28|05:19:58] Generating DAG in progress               epoch=1 percentage=93 elapsed=5h31m51.285s
INFO [03-28|05:20:03] Generating DAG in progress               epoch=1 percentage=94 elapsed=5h31m56.675s
INFO [03-28|05:20:08] Generating DAG in progress               epoch=1 percentage=95 elapsed=5h32m1.628s

```
分析一下:  
* Generating DAG in progress  
正在生成DAG，参考[DAG](https://www.jianshu.com/p/69a344b0ed32)

* Regenerated local transaction journal  
重新生成本地事务日志

* Successfully sealed new block  
成功密封新块

* mined potential block   
挖掘潜在的块

* Commit new mining work   
提交新的采矿工作

* block reached canonical chain  
块达到了规范链

* epoch  
纪元

* elapsed   
过去

* uncles   
叔链


这个时候我再查一下账户，发现已经有钱啦。这就是挖矿得来的奖励。  
我查了下总共有11次success的标识，钱包是55 ether，那每次挖矿会奖励5个以太币了。（显示的单位是wei）
```
> eth.getBalance(acc0)
55000000000000000000
```
> 注意：ethereum中的最小交易单位是wei，所有的交易都已Wei为单位进行。换算方法如下：

|Unit	|Wei Value	|Wei|
| - | :-: | -: |
|wei	| 1 |	1 wei|
|Kwei (babbage)	| 1e3 wei|	1,000|
|Mwei (lovelace)|	1e6 wei|	1,000,000|
|Gwei (shannon)	|1e9 wei	|1,000,000,000|
|microether (szabo)	|1e12 wei|	1,000,000,000,000|
|milliether (finney)|	1e15 wei|	1,000,000,000,000,000|
|ether|	1e18 wei|	1,000,000,000,000,000,000|


## 交易
为了模拟交易，我们新建一个用户。
```
> personal.newAccount()
Passphrase:
Repeat passphrase:
"0x68512ebacb81a3274933de1ac02a787927ef76d5"
> personal.listAccounts
["0x93a44e1e0aceb6a68ed39018d48a359b0beb8eb5", "0x68512ebacb81a3274933de1ac02a787927ef76d5"]
```
新账户的钱包：
```
> acc1 = eth.accounts[1]
"0x68512ebacb81a3274933de1ac02a787927ef76d5"
> eth.getBalance(acc1)
0
```

我们要互相转多少钱呢？由于wei的单位太小，直接使用起来不方便，我们可以执行以下命令：
```
> web3.toWei(1)
"1000000000000000000"
> web3.toWei(1,'ether')
"1000000000000000000"
> web3.toWei(1,'kWei')
"1000"
```
web3.toWei(number, unit) 是把数值转换为wei的单位  
web3.toWei(1,'ether') ===  1 ether (默认)  
web3.toWei(1,'kWei')=== 1 kWei

web3的用法参照[文档](https://github.com/ethereum/wiki/wiki/JavaScript-API#web3towei).

web3 console里内置了一些用来操作以太坊的Javascript对象。主要有：
* eth：包含一些跟操作区块链相关的方法
* net：包含以下查看p2p网络状态的方法
* admin：包含一些与管理节点相关的方法
* miner：包含启动&停止挖矿的一些方法
* personal：主要包含一些管理账户的方法
* txpool：包含一些查看交易内存池的方法
* web3：包含了以上对象，还包含一些单位换算的方法


我们把钱从第一个账户转到第二个账户，金额是1 ether。
```
> money = web3.toWei(1)
"1000000000000000000"
> eth.sendTransaction({from:acc0, to:acc1, value:money})
Error: authentication needed: password or unlock
    at web3.js:3143:20
    at web3.js:6347:15
    at web3.js:5081:36
    at <anonymous>:1:1
```
命令很好理解（命令省去了一些参数）。报错也很常见，账户被锁。这是ethereum的安全机制，每隔一段时间就会锁定账户。交易前先解锁账户（发起交易的账户）。

```
> personal.unlockAccount(acc0)
Unlock account 0x93a44e1e0aceb6a68ed39018d48a359b0beb8eb5
Passphrase:
true
```

解锁完成，直接操作
```
> eth.sendTransaction({from:acc0, to:acc1, value:web3.toWei(1, 'ether')})
INFO [03-29|02:37:55] Submitted transaction                    fullhash=0xde091e31d3251b590420981f7d1b5675bb18ef3a6abcbe91fe5565c6f4299f16 recipient=0x68512eBACB81a3274933DE1ac02a787927Ef76D5
"0xde091e31d3251b590420981f7d1b5675bb18ef3a6abcbe91fe5565c6f4299f16"
```

> 执行到这一步如果报错Error:insufficient funds for gas * price + value的报错，回到init时的attention处找一下原因。我做到这一步报错，停掉重新init，重新启动，账户还在，余额也在，转账正常。原理还未知。

这个时候，查看两个账户的余额，发现还没有变化。这是因为交易要记录在通过挖矿找到的块上才算生效。所以我们继续挖矿。

最终结果如下：挖到矿后，两个账户的金额有了变动，账户1:55+5（挖矿）-1（交易） ；账户2:1（交易）
```
> miner.start(10)
INFO [03-29|02:57:56] Updated mining threads                   threads=10
INFO [03-29|02:57:56] Transaction pool price threshold updated price=18000000000
null
> INFO [03-29|02:57:56] Starting mining operation
INFO [03-29|02:57:56] Commit new mining work                   number=12 txs=1 uncles=0 elapsed=109.261ms
INFO [03-29|03:03:00] Successfully sealed new block            number=12 hash=205b7a…159a50
INFO [03-29|03:03:01] 🔨 mined potential block                  number=12 hash=205b7a…159a50
INFO [03-29|03:03:01] Commit new mining work                   number=13 txs=0 uncles=0 elapsed=357.976µs
> miner.stop()
true
> eth.getBalance(acc0)
59000000000000000000
> eth.getBalance(acc1)
1000000000000000000
```


## 参考文档
1. https://blog.csdn.net/ethchinese/article/details/62220879
2. https://blog.csdn.net/lhtzbj12/article/details/79405238
3. https://github.com/ethereum/go-ethereum/wiki/Private-network
