---
title: geth console命令
categories:
  - notes
tags:
  - geth
  - console
  - ethereum
toc: true
date: 2018-04-05 12:46:45
---

geth console命令


<!-- more -->

# 描述

Geth控制台是JavaScript运行时环境的交互式shell，它暴露了一个节点管理界面以及Ðapp JavaScript API。  
请参阅 https://github.com/ethereum/go-ethereum/wiki/JavaScript-Console。

> 这大概是用的最多的geth命令了

# 基本用法
```
USAGE:
   geth console [command options] [arguments...]

ETHEREUM OPTIONS:
 --identity value             自定义节点名称
 --datadir "/root/.ethereum"  数据库和密钥库的数据目录
 --keystore                   密钥库目录 (默认同datadir)
 --nousb                      禁用监视和管理USB硬件钱包
 --syncmode "fast"            区块链同步模式 ("fast", "full", or "light")，参数必填，没有默认值
 --gcmode value               区块链垃圾收集模式 ("full", "archive") (默认: "full")
 --lightserv value            允许服务LES请求的最大时间百分比 (0-90) (默认: 0)
 --lightpeers value           LES客户端的最大数量 (默认: 100)
 --lightkdf                   以减少KDF（秘钥生成函数）的强度为代价减少生成秘钥时RAM和CPU的使用量
 --testnet                    Ropsten网络：预先配置的POW(proof-of-work)测试网络
 --rinkeby                    Rinkeby网络：预先配置的POA(proof-of-authority)测试网络
 --networkid value            网络标识符 (整数, 1=Frontier(以太坊线上网络：主网), 2=Morden (已废弃), 3=Ropsten, 4=Rinkeby) (默认: 1)
 --ethstats value             报告ethstats服务的URL (nodename:secret@host:port)
 --config value               TOML配置文件

DEVELOPER CHAIN OPTIONS:
 --dev               dev模式下包括一个短连的（Ephemeral）POA共识网络，一个预先分派的有资金的账户，且已自动开启挖矿。
 --dev.period value  在开发者模式下挖矿周期（生成区块的周期）（0 =仅在交易使用）（默认值：0）

ETHASH OPTIONS（ETHASH算法）:
 --ethash.cachedir                用于存储ethash验证缓存的目录 (默认同datadir)
 --ethash.cachesinmem value       保留在内存中的最新ethash缓存数量（每个16MB） (默认: 2)
 --ethash.cachesondisk value      保留在磁盘上的最新ethash缓存数量（每个16MB） (默认: 3)
 --ethash.dagdir "/root/.ethash"  用于存储ethash mining DAGs的目录 (默认同datadir)
 --ethash.dagsinmem value         保留在内存中的最新ethash mining DAGs的个数 (每个1GB+) (默认: 1)
 --ethash.dagsondisk value        保留在磁盘中的最新ethash mining DAGs的个数 (每个1GB+) (默认: 2)

TRANSACTION POOL OPTIONS（交易池选项）:
 --txpool.nolocals            本地提交交易禁用价格豁免
 --txpool.journal value       用于本地事务的磁盘日志以保持节点重新启动 Disk journal for local transaction to survive node restarts (默认: "transactions.rlp")
 --txpool.rejournal value     重新生成本地事务日志的时间间隔 (默认: 1h0m0s)
 --txpool.pricelimit value    加入交易池的最小的gas价格限制 (默认: 1)
 --txpool.pricebump value     价格颠簸百分比来替代已经存在的交易 Price bump percentage to replace an already existing transaction (默认: 10)
 --txpool.accountslots value  每个帐户保证的可执行交易时间的最小数量 Minimum number of executable transaction slots guaranteed per account (默认: 16)
 --txpool.globalslots value   所有帐户的可执行事务处理槽的最大数量 Maximum number of executable transaction slots for all accounts (默认: 4096)
 --txpool.accountqueue value  每个帐户允许的最大非可执行事务处理槽数量 Maximum number of non-executable transaction slots permitted per account (默认: 64)
 --txpool.globalqueue value   所有帐户的最大非可执行事务处理槽数 Maximum number of non-executable transaction slots for all accounts (默认: 1024)
 --txpool.lifetime value      不可执行事务排队的最长时间 Maximum amount of time non-executable transaction are queued (默认: 3h0m0s)

PERFORMANCE TUNING OPTIONS:
 --cache value            分配给内部缓存使用的内存（MB） (默认: 1024)
 --cache.database value   用于数据库io的缓存内存容量的百分比 Percentage of cache memory allowance to use for database io (默认: 75)
 --cache.gc value         缓存内存允许用于修剪的百分比 Percentage of cache memory allowance to use for trie pruning (默认: 25)
 --trie-cache-gens value  保存在内存中的trie node的数量 (默认: 120)

ACCOUNT OPTIONS:
 --unlock value    解锁以逗号分隔的帐户列表
 --password value  用于非交互式密码输入的密码文件

API AND CONSOLE OPTIONS:
 --rpccorsdomain value  以逗号分隔的接受跨源请求的域名列表（浏览器强制执行）
 --rpcvhosts value      以逗号分隔的用于接受请求的虚拟主机名列表（服务器强制执行）。 接入'*'通配符. (默认: "localhost")
 --rpc                  启用HTTP-RPC服务器
 --rpcaddr value        HTTP-RPC服务器监听地址 (默认: "localhost")
 --rpcport value        HTTP-RPC服务器监听端口 (默认: 8545)
 --rpcapi value         基于HTTP-RPC接口提供的API
 --ws                   启用WS-RPC服务器
 --wsaddr value         WS-RPC服务器监听地址 (默认: "localhost")
 --wsport value         WS-RPC服务器监听端口 (默认: 8546)
 --wsapi value          通过WS-RPC接口提供的API
 --wsorigins value      允许接受websockets请求的源
 --ipcdisable           禁用IPC-RPC服务器
 --ipcpath              数据区中IPC套接字/管道的文件名（显式路径将其转义） Filename for IPC socket/pipe within the datadir (explicit paths escape it)
 --jspath loadScript    设置加载JS脚本的根路径 (默认: ".")
 --exec value           执行JavaScript语句
 --preload value        把以逗号分隔的JavaScript文件列表预加载到控制台中

NETWORKING OPTIONS:
 --bootnodes value     用于P2P发现引导的enode urls(逗号分隔) (set v4+v5 instead for light servers)
 --bootnodesv4 value   用于P2P V4发现引导的enode urls(逗号分隔) (light server, full nodes)
 --bootnodesv5 value   用于P2P V5发现引导的enode urls(逗号分隔) (light server, light nodes)
 --port value          网络监听端口 (默认: 30303)
 --maxpeers value      最大的网络节点数量(如果设置为0，网络将被禁用) (默认: 25)
 --maxpendpeers value  最大尝试连接的数量  (如果设置为0，则将使用默认值) (默认: 0)
 --nat value           NAT端口映射机制（any | none | upnp | pmp | extip：<IP>） (默认: "any")
 --nodiscover          禁用节点发现机制 (手动添加节点)
 --v5disc              启用实验性RLPx V5 Enables the experimental RLPx V5 (Topic Discovery) mechanism
 --netrestrict value   将网络通信限制在给定的IP网络上 Restricts network communication to the given IP networks (CIDR masks)
 --nodekey value       P2P节点密钥文件
 --nodekeyhex value    十六进制的P2P节点密钥（用于测试）

MINER OPTIONS:
 --etherbase value         地块挖掘奖励的公开地址 Public address for block mining rewards (默认 = first account created) (默认: "0")
 --gasprice "18000000000"  最小的天然气价格，以接受采矿交易 Minimal gas price to accept for mining a transactions
 --minerthreads value      用于挖矿的CPU线程数 (默认: 4)
 --mine                    启用挖矿
 --targetgaslimit value    目标气体极限设置了要开采的块的人造目标气体地板 Target gas limit sets the artificial target gas floor for the blocks to mine (默认: 4712388)
 --extradata value         阻止矿工设置的额外数据 Block extra data set by the miner (默认 = client version)

GAS PRICE ORACLE OPTIONS:
 --gpoblocks value      检查天然气价格的最近区块数量 Number of recent blocks to check for gas prices (默认: 20)
 --gpopercentile value  建议的天然气价格是一组最近交易天然气价格的给定百分位数 Suggested gas price is the given percentile of a set of recent transaction gas prices (默认: 60)

VIRTUAL MACHINE OPTIONS:
 --vmdebug  记录对虚拟机和合同调试有用的信息 Record information useful for VM and contract debugging

LOGGING AND DEBUGGING OPTIONS:
 --metrics       启用指标收集和报告 Enable metrics collection and reporting
 --fakepow       禁用POW验证 Disables proof-of-work verification
 --nocompaction  导入后禁用数据库压缩 Disables db compaction after import

WHISPER (EXPERIMENTAL) OPTIONS:
 --shh                       启用耳语 Enable Whisper
 --shh.maxmessagesize value  接受最大邮件大小 Max message size accepted (默认: 1048576)
 --shh.pow value             Minimum POW accepted (默认: 0.2)

DEPRECATED OPTIONS（弃用的选项）:
 --fast   Enable fast syncing through state downloads
 --light  Enable light client mode

MISC OPTIONS:
 --dashboard                启用仪表板
 --dashboard.addr value     仪表板监听地址 (默认: "localhost")
 --dashboard.host value     仪表板监听端口 (默认: 8080)
 --dashboard.refresh value  仪表板指标收集刷新频率 (默认: 5s)
 --dashboard.assets value   开发人员标志从本地文件系统提供仪表板 Developer flag to serve the dashboard from the local file system
```

# 示例
## 以太坊选项（ETHEREUM OPTIONS）

### identity

```
> geth console --identity "myTestChain"
```
其它选项相同，添加了indentity属性，geth启动后的区别如下：
```
//不加identity
instance: Geth/v1.8.2-stable-b8b9f7f4/linux-amd64/go1.9.4

// identity
instance: Geth/myTestChain/v1.8.2-stable-b8b9f7f4/linux-amd64/go1.9.4
```

### nousb
这部分不太了解。猜测USB钱包是类似于U盾一样的东西。


### syncmode
3种同步模式，参考[Question](https://ethereum.stackexchange.com/questions/1161/what-is-geths-fast-sync-and-why-is-it-faster)
* fast：快速模式，仅下载所有区块的状态信息，状态下载到当前块之前不处理任何事务。状态下载完成后，下载同full模式。一般这个选项配合cache（设置为1G或2G）一起使用，加快下载
* light： 轻客户端模式，仅获取当前状态
* full(默认)：完整模式，下载所有区块数据信息，从创世块开始逐一校验下载


# 参考文献
* https://blog.csdn.net/wo541075754/article/details/78360560
* https://juejin.im/entry/5a1e174ff265da43322753a6
