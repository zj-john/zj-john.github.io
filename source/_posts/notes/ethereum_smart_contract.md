---
title: ethereum 智能合约
categories:
  - notes
tags:
  - ethereum
  - smart contract
toc: true
date: 2018-03-30 14:20:51
---

blockchain_smart_contract

ethereum smart contract的编写

<!-- more -->

## 智能合约讲解
Demo来自solidity的[官网](http://solidity.readthedocs.io/en/latest/solidity-by-example.html)，制定了一个带有委托功能的投票（选举）智能合约。

逐行讲解代码，以便大家了解solidity语法和合约功能。

```C++
// 当前使用的solidity的版本号 ^符号的意义和npm版本中相同
pragma solidity ^0.4.16;

/// 带有委托功能的投票契约
contract Ballot {
    // 声明了一个struct类型：Voter，用来代表每一个单独的voter。可以理解为抽象了一个voter的类。下文简称之为对象。
    struct Voter {
        // 权重，由于可以委托，权重可能大于1
        uint weight;
        // 标识是否有投票权，如果为true，标识已经投过票，不能再投
        bool voted;
        // 被委托人的地址
        address delegate;
        // 支持的（已投票的）提议的序号
        uint vote;
    }

    // 声明一个代表提议案的struct类型
    struct Proposal {
        // 提议案的name，最大32bytes
        bytes32 name;
        // 获得的投票数
        uint voteCount;
    }

    // 召集这次投票的主人，可以决定哪些人有投票权。public类型，表示可以address可以被外部知晓，这里称之为主席
    address public chairperson;

    // 一个叫做voters的public变量，为每一个address对应生成一个Voter对象
    mapping(address => Voter) public voters;

    // 一个储存Proposal的public对象的数组
    Proposal[] public proposals;

    // 发起一个投票，从若干个proposalNames（Array类型）中选择你支持的那个，public的，可以被外部查看到
    function Ballot(bytes32[] proposalNames) public {
        // 调用这个方法的人就是主席，把他的公钥赋给chairperson
        chairperson = msg.sender;
        // 他自己也有一次投票权，给他的voter的权重加1
        voters[chairperson].weight = 1;

        // 为每一个proposalNames下的元素生成对应的proposal对象，添加到proposals对象中
        for (uint i = 0; i < proposalNames.length; i++) {
            proposals.push(Proposal({
                name: proposalNames[i],
                voteCount: 0
            }));
        }
    }

    // 主席分发投票权
    function giveRightToVote(address voter) public {
        // 如果require中的参数等于false，将直接终止运行,并回退所有状态的改变和以太币的交易。所以要在一个函数未被正确调用时使用这个方法
        // 另外，这个回退操作也会消耗gas（后续的版本打算修复这一情况）
        require(
            (msg.sender == chairperson) &&
            !voters[voter].voted &&
            (voters[voter].weight == 0)
        );
        voters[voter].weight = 1;
    }

    // 把投票权委托给to的那个人（公钥）
    function delegate(address to) public {
        // storage类型的对象将永远存在区块链上，前提是没有回退
        Voter storage sender = voters[msg.sender];
        require(!sender.voted);

        // 自己给自己授权不被允许
        require(to != msg.sender);

        // 一般来说，循环是很危险的，如果运行时间过长，很有可能导致gas消耗完毕，使合约卡主。
        // 如果被委托人也有他自己的委托人，就向上查找到最终的委托人
        // address(0)等于"0x0",一个未被使用的地址
        while (voters[to].delegate != address(0)) {
            to = voters[to].delegate;

            // 避免死循环
            require(to != msg.sender);
        }

        sender.voted = true;
        sender.delegate = to;
        Voter storage delegate_ = voters[to];
        if (delegate_.voted) {
            // 如果被委托人已经投过票，他投的票上加上委托人拥有的权重
            proposals[delegate_.vote].voteCount += sender.weight;
        } else {
            // 如果还没投票，把他的weight提高
            delegate_.weight += sender.weight;
        }
    }

    // 行使投票权
    function vote(uint proposal) public {
        Voter storage sender = voters[msg.sender];
        require(!sender.voted);
        sender.voted = true;
        sender.vote = proposal;

        // 如果proposal不在数组中，会自动抛异常然后回退
        proposals[proposal].voteCount += sender.weight;
    }

    // 统计票数，返回获胜者的序号
    // 函数加view，表示这个函数中承诺不更改状态
    function winningProposal() public view
            returns (uint winningProposal_)
    {
        uint winningVoteCount = 0;
        for (uint p = 0; p < proposals.length; p++) {
            if (proposals[p].voteCount > winningVoteCount) {
                winningVoteCount = proposals[p].voteCount;
                winningProposal_ = p;
            }
        }
    }

    // 获取获胜提议的name
    function winnerName() public view
            returns (bytes32 winnerName_)
    {
        winnerName_ = proposals[winningProposal()].name;
    }
}
```


## 从最简单开始
合约[地址](http://solidity.readthedocs.io/en/latest/introduction-to-smart-contracts.html#a-simple-smart-contract)

```c
pragma solidity ^0.4.0;

contract SimpleStorage {
    uint storedData;

    function set(uint x) public {
        storedData = x;
    }

    function get() public constant returns (uint) {
        return storedData;
    }
}
```

打开合约在线[编译地址](http://remix.ethereum.org/#optimize=false&version=soljson-v0.4.21+commit.dfe3193c.js),把上面的合约复制进去，点击右侧的“Start to compile”

如果所示，编译成功。
![](/images/remix.jpg)

我们点击Detail，打开编译的详情。我们需要的部分如下：
![](/images/remix_detail.jpg)

我们copy WEB3DEPLOY中的内容(2个JS变量)，粘贴在geth的命令行中。
```js
var simplestorageContract = web3.eth.contract([{"constant":false,"inputs":[{"name":"x","type":"uint256"}],"name":"set","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"get","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"}]);
var simplestorage = simplestorageContract.new(
   {
     from: web3.eth.accounts[0],
     data: '0x6060604052341561000f57600080fd5b60d38061001d6000396000f3006060604052600436106049576000357c0100000000000000000000000000000000000000000000000000000000900463ffffffff16806360fe47b114604e5780636d4ce63c14606e575b600080fd5b3415605857600080fd5b606c60048080359060200190919050506094565b005b3415607857600080fd5b607e609e565b6040518082815260200191505060405180910390f35b8060008190555050565b600080549050905600a165627a7a72305820d260b3fab2aaf9b7911618aac87abe44b9a999f906eeac6e177fd8df4ba06a3d0029',
     gas: '4700000'
   }, function (e, contract){
    console.log(e, contract);
    if (typeof contract.address !== 'undefined') {
         console.log('Contract mined! address: ' + contract.address + ' transactionHash: ' + contract.transactionHash);
    }
 })
```
粘贴后提示先解锁账户，我们解锁账户0。
```
personal.unlockAccount(acc0)
```
之后又有报错：Error: exceeds block gas limit undefined,我们采用[文档](https://blog.csdn.net/loy_184548/article/details/78002015)的说法把gas:'4700000'改为gas:'3000000'

结果如下：
```
INFO [03-30|00:04:41] Submitted contract creation              fullhash=0x9b1b99ad03b577839547127304c7e149eaa50d5deb932c5f25b285005ea8edbc contract=0xEd9F8F66fFac7FF317b4f10d48721d36E111a308

null [object Object]
undefined
> simplestorage
{
  abi: [{
      constant: false,
      inputs: [{...}],
      name: "set",
      outputs: [],
      payable: false,
      stateMutability: "nonpayable",
      type: "function"
  }, {
      constant: true,
      inputs: [],
      name: "get",
      outputs: [{...}],
      payable: false,
      stateMutability: "view",
      type: "function"
  }],
  address: undefined,
  transactionHash: "0x9b1b99ad03b577839547127304c7e149eaa50d5deb932c5f25b285005ea8edbc"
}
```

得到了两个值
* 交易哈希：  
fullhash=0x9b1b99ad03b577839547127304c7e149eaa50d5deb932c5f25b285005ea8edbc
* 合约地址：  
contract=0xEd9F8F66fFac7FF317b4f10d48721d36E111a308


现在需要挖矿把合约写到区块链中。
```
> miner.start(10)
INFO [03-30|00:20:40] Updated mining threads                   threads=10
INFO [03-30|00:20:40] Transaction pool price threshold updated price=18000000000
null
INFO [03-30|00:20:40] Starting mining operation
INFO [03-30|00:20:40] Commit new mining work                   number=13 txs=1 uncles=0 elapsed=387.643ms
INFO [03-30|00:25:08] Successfully sealed new block            number=13 hash=c1c3b2…d683af
INFO [03-30|00:25:08] 🔨 mined potential block                  number=13 hash=c1c3b2…d683af
INFO [03-30|00:25:09] Commit new mining work                   number=14 txs=0 uncles=0 elapsed=15.773ms
null [object Object]
Contract mined! address: 0xed9f8f66ffac7ff317b4f10d48721d36e111a308 transactionHash: 0x9b1b99ad03b577839547127304c7e149eaa50d5deb932c5f25b285005ea8edbc
```

看到Contract mined!，表示合约已经写到链上了。

调用一次合约：
```
> personal.unlockAccount(acc0)
Unlock account 0x93a44e1e0aceb6a68ed39018d48a359b0beb8eb5
Passphrase:
true
> simplestorage.set(1106,{from:acc0})
INFO [03-30|00:58:34] Submitted transaction                    fullhash=0x945de4c8424e1c2675d90e1f03f13ad56fd329460d32f988b3cbf0c6ff9270b7 recipient=0xEd9F8F66fFac7FF317b4f10d48721d36E111a308
"0x945de4c8424e1c2675d90e1f03f13ad56fd329460d32f988b3cbf0c6ff9270b7"
```

调用合约中的set方法，把值设置为1106，这笔操作的gas费用从acc0账户出。执行成功后，得到"0x945de4c8424e1c2675d90e1f03f13ad56fd329460d32f988b3cbf0c6ff9270b7"这个hash值，是交易的哈希。

现在要继续挖矿，把这次的结果写入链中，才能看到生效。  
挖矿成功后：
```
> simplestorage.get()
1106
```
刚才的数据已经set成功。get属于constant类型，不改变状态，所以不消耗gas。

## 找回合约
如果把geth关掉了，怎么找回合约呢？  
我们刚才使用的是WEB3DEPLOY中内容，现在使用另一个ABI的值。

```
[
	{
		"constant": false,
		"inputs": [
			{
				"name": "x",
				"type": "uint256"
			}
		],
		"name": "set",
		"outputs": [],
		"payable": false,
		"stateMutability": "nonpayable",
		"type": "function"
	},
	{
		"constant": true,
		"inputs": [],
		"name": "get",
		"outputs": [
			{
				"name": "",
				"type": "uint256"
			}
		],
		"payable": false,
		"stateMutability": "view",
		"type": "function"
	}
]
```

geth中执行（把回车符去除，否则会截断报错）
```
var abi = [{"constant": false,"inputs": [{"name": "x","type": "uint256"}],"name": "set","outputs": [],"payable": false,"stateMutability": "nonpayable","type": "function"},{"constant": true,"inputs": [],"name": "get","outputs": [	{"name": "","type": "uint256"	}],"payable": false,"stateMutability": "view","type": "function"}];
var simplestorage = eth.contract(abi).at("0xEd9F8F66fFac7FF317b4f10d48721d36E111a308")
```
0xEd9F8F66fFac7FF317b4f10d48721d36E111a308是上文记录的合约地址。  
这样就把合约再次创建出来了。  
我们之前操作的结果还在。  
```
Contract = eth.contract(abi) //获取合约对象
Contract.at(contract.address) //实例化合约
```


```
> simplestorage.get()
1106
```
