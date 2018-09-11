---
title: ethereum 报错汇总
categories:
  - notes
tags:
  - ethereum
  - error
toc: false
date: 2018-04-02 14:20:51
---

ethereum 报错汇总

包含一些自己遇到的和网上收集的报错。

<!-- more -->
* invalid sender undefined  
这个错误不会导致初始化失败，但是会在以后的转账（eth.sendTransaction），或者部署智能合约的时候产生。解决方法就是在genesis.json文件中，chainId 不能设置为0。 如果你完全按照github上给的官方配置文件，就会产生这个错误


* Error: authentication needed: password or unlock undefined  
解锁账户：personal.unlockAccount(acc0)

* Error: exceeds block gas limit undefined.The contract code couldn't be stored, please check your gas amount. undefined  
把合约中gas改为300000。直接从那个网站的Web3 deploy 复制可能是4300000，然后改成300000，就没问题了。

* Fatal: invalid genesis file: missing 0x prefix for hex data  
这个错误信息意思很明白，就是你的json文件中，对于16进制数据，需要加上0x前缀

* Fatal: invalid genesis file: hex string has odd length  
从v1.6开始，设置的十六进制数值，不能是奇数位， 比如不能是0x0，而应该是0x00。

* Fatal: failed to write genesis block: genesis has no chain configuration  
这个错误信息，就是说，你的json文件中，缺少config部分。看到这个信息，我们不需要把geth退回到v1.5版本，而是需要加上config部分



参考文档：
1. https://blog.csdn.net/loy_184548/article/details/78002015
2. http://blog.leanote.com/post/chaim/%E4%BB%A5%E5%A4%AA%E5%9D%8A%E7%A0%94%E7%A9%B6%E3%80%90%E5%AE%89%E8%A3%85%E3%80%91
3. https://github.com/ethereum/go-ethereum/issues/15983
