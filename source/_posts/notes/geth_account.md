---
title: geth account命令
categories:
  - notes
tags:
  - geth
  - account
  - console
  - ethereum
toc: true
date: 2018-04-02 14:20:02
---

geth account命令


<!-- more -->

# 描述

功能：管理帐户
* 列出所有现有帐户
* 将私钥导入新帐户
* 创建一个新帐户或更新现有帐户

当系统提示您输入密码时，它支持交互模式。当通过给定的密码文件提供密码时转为非交互模式。非交互模式仅适用于在测试网络或已知安全的环境下的脚本使用。

> 记得你创建一个新账户时使用的密码（无论是新增账户还是导入账户）。没有它，你无法解锁你的账户。

请注意，不支持以非加密格式导出您的密钥。

密钥存储在<DATADIR> / keystore下。
以太坊节点之间，可以通过简单的复制转移整个目录或其中的个人密钥。这种行为是安全的。

> 定期备份你的钥匙。

# 基本用法
```
USAGE:
   geth account command [command options] [arguments...]

COMMANDS:
     list    列出所有帐户的简要信息
     new     创建一个新账户
     update  更新现有账户
     import  将私钥导入一个新帐户

OPTIONS:
   --help, -h  show help
```


## geth account list
列出所有帐户的简要信息
```
list [command options] [arguments...]

列出所有帐户的简要信息

ETHEREUM OPTIONS:
  --datadir "/root/.ethereum"  数据库和密钥库的数据目录
  --keystore                   密钥库目录 (默认同datadir)

```
示例：
```
> geth account list --datadir "/root/Documents/ethereum/data/"
INFO [04-09|00:56:47] Maximum peer count                       ETH=25 LES=0 total=25
Account #0: {93a44e1e0aceb6a68ed39018d48a359b0beb8eb5} keystore:///root/Documents/ethereum/data/keystore/UTC--2018-03-28T06-14-55.548233530Z--93a44e1e0aceb6a68ed39018d48a359b0beb8eb5
Account #1: {68512ebacb81a3274933de1ac02a787927ef76d5} keystore:///root/Documents/ethereum/data/keystore/UTC--2018-03-29T07-38-29.214491449Z--68512ebacb81a3274933de1ac02a787927ef76d5

```



## geth account new
```
new [command options] [arguments...]

    geth account new


创建一个新帐户并打印地址。
该帐户以加密格式保存，系统会提示您输入密码。
您必须记住此密码才能在未来解锁您的帐户。

对于非交互式使用，可以使用--password 选项来指定密码
请注意，这种方式仅用于测试，因为将密码保存到文件或以任何其他方式公开都是一个坏主意。

ETHEREUM OPTIONS:
  --datadir "/root/.ethereum"  数据库和密钥库的数据目录
  --keystore                   密钥库目录 (默认同datadir)
  --lightkdf                   以减少KDF（秘钥生成函数）的强度为代价减少生成秘钥时RAM和CPU的使用量

ACCOUNT OPTIONS:
  --password value  用于非交互式密码输入时指定密码文件

```
示例：
```
> geth account new --datadir "/root/Documents/ethereum/data/"
INFO [04-09|01:15:46] Maximum peer count                       ETH=25 LES=0 total=25
Your new account is locked with a password. Please give a password. Do not forget this password.
Passphrase:
Repeat passphrase:
Address: {0ed90cf4d6a37cbcf7c4b9461f151c6f1fe2284b}

> geth account list --datadir "/root/Documents/ethereum/data/"
INFO [04-09|01:16:09] Maximum peer count                       ETH=25 LES=0 total=25
Account #0: {93a44e1e0aceb6a68ed39018d48a359b0beb8eb5} keystore:///root/Documents/ethereum/data/keystore/UTC--2018-03-28T06-14-55.548233530Z--93a44e1e0aceb6a68ed39018d48a359b0beb8eb5
Account #1: {68512ebacb81a3274933de1ac02a787927ef76d5} keystore:///root/Documents/ethereum/data/keystore/UTC--2018-03-29T07-38-29.214491449Z--68512ebacb81a3274933de1ac02a787927ef76d5
Account #2: {0ed90cf4d6a37cbcf7c4b9461f151c6f1fe2284b} keystore:///root/Documents/ethereum/data/keystore/UTC--2018-04-09T08-15-58.475205640Z--0ed90cf4d6a37cbcf7c4b9461f151c6f1fe2284b

```

使用密码文件的方式:  
/root/Documents/ethereum/pwd 的内容是一个密码的字符串
```
> geth account new --datadir "/root/Documents/ethereum/data/" --password "/root/Documents/ethereum/pwd"
INFO [04-09|01:17:44] Maximum peer count                       ETH=25 LES=0 total=25
Address: {f96a0654d887881cd8cdd5c4320125ae90515a0d}

> geth account list --datadir "/root/Documents/ethereum/data/"
INFO [04-09|01:17:49] Maximum peer count                       ETH=25 LES=0 total=25
Account #0: {93a44e1e0aceb6a68ed39018d48a359b0beb8eb5} keystore:///root/Documents/ethereum/data/keystore/UTC--2018-03-28T06-14-55.548233530Z--93a44e1e0aceb6a68ed39018d48a359b0beb8eb5
Account #1: {68512ebacb81a3274933de1ac02a787927ef76d5} keystore:///root/Documents/ethereum/data/keystore/UTC--2018-03-29T07-38-29.214491449Z--68512ebacb81a3274933de1ac02a787927ef76d5
Account #2: {0ed90cf4d6a37cbcf7c4b9461f151c6f1fe2284b} keystore:///root/Documents/ethereum/data/keystore/UTC--2018-04-09T08-15-58.475205640Z--0ed90cf4d6a37cbcf7c4b9461f151c6f1fe2284b
Account #3: {f96a0654d887881cd8cdd5c4320125ae90515a0d} keystore:///root/Documents/ethereum/data/keystore/UTC--2018-04-09T08-17-44.504001170Z--f96a0654d887881cd8cdd5c4320125ae90515a0d

```



## geth account update
```
update [command options] [arguments...]

    geth account update <address>

更新现有帐户。

要更新的帐户以加密格式保存在最新版本中，系统会提示您输入密码以解锁帐户，然后再输入新密码，就会把新密码更新到存储文件。

使用本命令可以用于将老格式的帐户迁移至最新格式或更改帐户的密码。

对于非交互式使用，可以使用--password 选项来指定新密码：

         geth account update [options] <address>

由于只能输入一个密码，只能进行格式更新，所以更改密码是唯一的交互方式。

ETHEREUM OPTIONS:
  --datadir "/root/.ethereum"  数据库和密钥库的数据目录
  --keystore                   密钥库目录 (默认同datadir)
  --lightkdf                   以减少KDF（秘钥生成函数）的强度为代价减少生成秘钥时RAM和CPU的使用量

```

> 我理解的这里的格式修改，是指随着geth版本升级，对账户的构成方式（生成秘钥的方式等）发生了变化，此时需要适配新版本。把老版本用户进行更新。实际中未遇到此类情况。欢迎理解的童鞋不吝赐教。

示例：
```
> geth account update f96a0654d887881cd8cdd5c4320125ae90515a0d --datadir "/root/Documents/ethereum/data/"
INFO [04-09|01:36:07] Maximum peer count                       ETH=25 LES=0 total=25
Unlocking account f96a0654d887881cd8cdd5c4320125ae90515a0d | Attempt 1/3
Passphrase:
INFO [04-09|01:36:11] Unlocked account                         address=0xf96a0654D887881cd8cDD5c4320125aE90515A0D
Please give a new password. Do not forget this password.
Passphrase:
Repeat passphrase:
```


## geth account import
```
import [command options] [arguments...]

    geth account import <keyfile>

从<keyfile>导入一个未加密的私钥并创建一个新帐户。返回新创建用户的地址。

这个密钥文件（keyfile）需要包含十六进制格式的未加密私钥。

该帐户将以加密格式保存，系统会提示您输入密码。您必须记住此密码才能在未来解锁您的帐户。

对于非交互式使用，可以使用--password 选项来指定新密码：

    geth account import [options] <keyfile>

注意：
由于您可以直接将加密帐户复制到另一个以太坊实例，因此在节点之间传输帐户时不需要此导入机制。

ETHEREUM OPTIONS:
  --datadir "/root/.ethereum"  数据库和密钥库的数据目录
  --keystore                   密钥库目录 (默认同datadir)
  --lightkdf                   以减少KDF（秘钥生成函数）的强度为代价减少生成秘钥时RAM和CPU的使用量

ACCOUNT OPTIONS:
  --password value  用于非交互式密码输入时指定密码文件

```

> 没有找到合适的key file，也未找到此命令的使用场景。
