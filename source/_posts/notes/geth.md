---
title: geth命令行
categories:
  - notes
tags:
  - geth
  - console
  - ethereum
toc: true
date: 2018-04-01 11:44:14
---

geth命令行


<!-- more -->

# 基本用法
```
geth [options] command [command options] [arguments...]
```

# 命令（COMMANDS）
```
account     管理账户     
attach      启动交互式JavaScript环境（连接到node）
bug         打开一个窗口（go-ethereum仓库中提交issue的地址），报告geth使用中的bug
console     启动交互式JavaScript环境
copydb      以目标目录的chaindata来创建一个本地链
dump        Dump a specific block from storage
dumpconfig  Show configuration values
export      把区块链导出到文件中
import      从区块链文件中导入
init        Bootstrap and initialize a new genesis block
js          执行指定的JavaScript文件
license     展示license信息
makecache   Generate ethash verification cache (for testing)
makedag     Generate ethash mining DAG (for testing)
monitor     Monitor and visualize node metrics
removedb    Remove blockchain and state databases
version     打印版本号
wallet      Manage Ethereum presale wallets
help, h     展示命令列表或者针对某一命令展示帮助信息
```


# 以太坊选项（ETHEREUM OPTIONS）
```
--config value               TOML configuration file
--datadir "/root/.ethereum"  数据库和密钥库的数据目录
--keystore                   密钥库目录 (默认同datadir)
--nousb                      Disables monitoring for and managing USB hardware wallets
--networkid value            Network identifier (integer, 1=Frontier, 2=Morden (disused), 3=Ropsten, 4=Rinkeby) (default: 1)
--testnet                    Ropsten network: pre-configured proof-of-work test network
--rinkeby                    Rinkeby network: pre-configured proof-of-authority test network
--syncmode "fast"            Blockchain sync mode ("fast", "full", or "light")
--gcmode value               Blockchain garbage collection mode ("full", "archive") (default: "full")
--ethstats value             Reporting URL of a ethstats service (nodename:secret@host:port)
--identity value             Custom node name
--lightserv value            Maximum percentage of time allowed for serving LES requests (0-90) (default: 0)
--lightpeers value           Maximum number of LES client peers (default: 100)
--lightkdf                   以减少KDF（秘钥生成函数）的强度为代价减少生成秘钥时RAM和CPU的使用量
```

# DEVELOPER CHAIN OPTIONS:
```
* --dev               Ephemeral proof-of-authority network with a pre-funded developer account, mining enabled
* --dev.period value  Block period to use in developer mode (0 = mine only if transaction pending) (default: 0)
```

# ETHASH OPTIONS:
```
* --ethash.cachedir                Directory to store the ethash verification caches (default = inside the datadir)
* --ethash.cachesinmem value       Number of recent ethash caches to keep in memory (16MB each) (default: 2)
* --ethash.cachesondisk value      Number of recent ethash caches to keep on disk (16MB each) (default: 3)
* --ethash.dagdir "/root/.ethash"  Directory to store the ethash mining DAGs (default = inside home folder)
* --ethash.dagsinmem value         Number of recent ethash mining DAGs to keep in memory (1+GB each) (default: 1)
* --ethash.dagsondisk value        Number of recent ethash mining DAGs to keep on disk (1+GB each) (default: 2)
```

# TRANSACTION POOL OPTIONS:
```
* --txpool.nolocals            Disables price exemptions for locally submitted transactions
* --txpool.journal value       Disk journal for local transaction to survive node restarts (default: "transactions.rlp")
* --txpool.rejournal value     Time interval to regenerate the local transaction journal (default: 1h0m0s)
* --txpool.pricelimit value    Minimum gas price limit to enforce for acceptance into the pool (default: 1)
* --txpool.pricebump value     Price bump percentage to replace an already existing transaction (default: 10)
* --txpool.accountslots value  Minimum number of executable transaction slots guaranteed per account (default: 16)
* --txpool.globalslots value   Maximum number of executable transaction slots for all accounts (default: 4096)
* --txpool.accountqueue value  Maximum number of non-executable transaction slots permitted per account (default: 64)
* --txpool.globalqueue value   Maximum number of non-executable transaction slots for all accounts (default: 1024)
* --txpool.lifetime value      Maximum amount of time non-executable transaction are queued (default: 3h0m0s)
```

# PERFORMANCE TUNING OPTIONS:
```
* --cache value            Megabytes of memory allocated to internal caching (default: 1024)
* --cache.database value   Percentage of cache memory allowance to use for database io (default: 75)
* --cache.gc value         Percentage of cache memory allowance to use for trie pruning (default: 25)
* --trie-cache-gens value  Number of trie node generations to keep in memory (default: 120)
```

# ACCOUNT OPTIONS:
```
* --unlock value    Comma separated list of accounts to unlock
* --password value  用于非交互式密码输入时指定密码文件
```

# API AND CONSOLE OPTIONS:
```
* --rpc                  Enable the HTTP-RPC server
* --rpcaddr value        HTTP-RPC server listening interface (default: "localhost")
* --rpcport value        HTTP-RPC server listening port (default: 8545)
* --rpcapi value         API's offered over the HTTP-RPC interface
* --ws                   Enable the WS-RPC server
* --wsaddr value         WS-RPC server listening interface (default: "localhost")
* --wsport value         WS-RPC server listening port (default: 8546)
* --wsapi value          API's offered over the WS-RPC interface
* --wsorigins value      Origins from which to accept websockets requests
* --ipcdisable           Disable the IPC-RPC server
* --ipcpath              Filename for IPC socket/pipe within the datadir (explicit paths escape it)
* --rpccorsdomain value  Comma separated list of domains from which to accept cross origin requests (browser enforced)
* --rpcvhosts value      Comma separated list of virtual hostnames from which to accept requests (server enforced). Accepts '*' wildcard. (default: "localhost")
* --jspath loadScript    JavaScript root path for loadScript (default: ".")
* --exec value           Execute JavaScript statement
* --preload value        Comma separated list of JavaScript files to preload into the console
```

# NETWORKING OPTIONS:
```
* --bootnodes value     Comma separated enode URLs for P2P discovery bootstrap (set v4+v5 instead for light servers)
* --bootnodesv4 value   Comma separated enode URLs for P2P v4 discovery bootstrap (light server, full nodes)
* --bootnodesv5 value   Comma separated enode URLs for P2P v5 discovery bootstrap (light server, light nodes)
* --port value          Network listening port (default: 30303)
* --maxpeers value      Maximum number of network peers (network disabled if set to 0) (default: 25)
* --maxpendpeers value  Maximum number of pending connection attempts (defaults used if set to 0) (default: 0)
* --nat value           NAT port mapping mechanism (any|none|upnp|pmp|extip:<IP>) (default: "any")
* --nodiscover          Disables the peer discovery mechanism (manual peer addition)
* --v5disc              Enables the experimental RLPx V5 (Topic Discovery) mechanism
* --netrestrict value   Restricts network communication to the given IP networks (CIDR masks)
* --nodekey value       P2P node key file
* --nodekeyhex value    P2P node key as hex (for testing)
```

# MINER OPTIONS:
```
* --mine                    Enable mining
* --minerthreads value      Number of CPU threads to use for mining (default: 4)
* --etherbase value         Public address for block mining rewards (default = first account created) (default: "0")
* --targetgaslimit value    Target gas limit sets the artificial target gas floor for the blocks to mine (default: 4712388)
* --gasprice "18000000000"  Minimal gas price to accept for mining a transactions
* --extradata value         Block extra data set by the miner (default = client version)
```

# GAS PRICE ORACLE OPTIONS:
```
* --gpoblocks value      Number of recent blocks to check for gas prices (default: 20)
* --gpopercentile value  Suggested gas price is the given percentile of a set of recent transaction gas prices (default: 60)
```

# VIRTUAL MACHINE OPTIONS:
```
* --vmdebug  Record information useful for VM and contract debugging
```

# LOGGING AND DEBUGGING OPTIONS:
```
* --metrics                 Enable metrics collection and reporting
* --fakepow                 Disables proof-of-work verification
* --nocompaction            Disables db compaction after import
* --verbosity value         Logging verbosity: 0=silent, 1=error, 2=warn, 3=info, 4=debug, 5=detail (default: 3)
* --vmodule value           Per-module verbosity: comma-separated list of <pattern>=<level> (e.g. eth/*=5,p2p=4)
* --backtrace value         Request a stack trace at a specific logging statement (e.g. "block.go:271")
* --debug                   Prepends log messages with call-site location (file and line number)
* --pprof                   Enable the pprof HTTP server
* --pprofaddr value         pprof HTTP server listening interface (default: "127.0.0.1")
* --pprofport value         pprof HTTP server listening port (default: 6060)
* --memprofilerate value    Turn on memory profiling with the given rate (default: 524288)
* --blockprofilerate value  Turn on block profiling with the given rate (default: 0)
* --cpuprofile value        Write CPU profile to the given file
* --trace value             Write execution trace to the given file
```

# WHISPER (EXPERIMENTAL) OPTIONS:
```
* --shh                       Enable Whisper
* --shh.maxmessagesize value  Max message size accepted (default: 1048576)
* --shh.pow value             Minimum POW accepted (default: 0.2)
```

# DEPRECATED OPTIONS:
```
* --fast   Enable fast syncing through state downloads
* --light  Enable light client mode
```

# MISC OPTIONS:
```
* --help, -h  show help
```
