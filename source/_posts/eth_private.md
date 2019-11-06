---
title: ETH私链
---

# 搭建私链
## 下载安装
[https://gethstore.blob.core.windows.net/builds/geth-windows-amd64-1.8.27-4bcc0a37.exe](https://gethstore.blob.core.windows.net/builds/geth-windows-amd64-1.8.27-4bcc0a37.exe)
生成两个文件
{% img /image/eth_pri_install.png "eth_pri_install" %}

## 准备创世区块配置文件
`genesis.json`

```bash
{
   "config": {
        "chainId": 22222,
        "homesteadBlock": 0,
        "eip155Block": 0,
        "eip158Block": 0
    },
    "coinbase" : "0x0000000000000000000000000000000000000000",
    "difficulty" : "0x01",
    "extraData" : "",
    "gasLimit" : "0xffffffff",
    "nonce" : "0x0000000000000042",
    "mixhash" : "0x0000000000000000000000000000000000000000000000000000000000000000",
    "parentHash" : "0x0000000000000000000000000000000000000000000000000000000000000000",
    "timestamp" : "0x00",
    "alloc": { }
}
```
| 初始化命令 | 作用                                                         |
| ---------- | ------------------------------------------------------------ |
| mixhash    | 与nonce配合用于挖矿，由上一个区块的一部分生成的hash          |
| nonce      | nonce就是一个64位随机数，用于挖矿                            |
| difficulty | 设置当前区块的难度，如果难度过大，cpu挖矿就很难，这里设置较小难度 |
| alloc      | 用来预置账号以及账号的以太币数量，因为私有链挖矿比较容易，所以我们不需要预置有币的账号，需要的时候自己创建即可以 |
| coinbase   | 矿工的账号，随便填                                           |
| timestamp  | 设置创世块的时间戳                                           |
| parentHash | 上一个区块的hash值，因为是创世块，所以这个值是0              |
| extraData  | 附加信息，随便填，可以填你的个性信息                         |
| gasLimit   | 该值设置对GAS的消耗总量限制，用来限制区块能包含的交易信息总和，因为我们是私有链，所以填最大 |
## 初始化创世区块
`geth --datadir "D:\Tool\ETH\data" init "D:\Tool\ETH\genesis.json" console`
如果在执行上面的命令报错，先删除原来的创世块`geth removedb --datadir data`

{% img /image/eth_pri_init.png "eth_pri_init" %}
初始化成功后，会在数据目录 data 中生成 geth 和 keystore 两个文件夹，此时目录结构如下：
{% img /image/eth_pri_path.png "eth_pri_path" %}

# 常用命令
## 启动geth
`geth --datadir "D:\Tool\ETH\data" console --rpc`
{% img /image/eth_pri_console.png "eth_pri_console" %}

## 创建账户
`personal.newAccount('Your Password')`
```bash
> personal.newAccount("1")
"0x12a97a88c217890aea39918b989b7ae1027295ad"
```
## 查看地址列表
```bash
> eth.accounts
["0x12a97a88c217890aea39918b989b7ae1027295ad", "0x0dd6f494848afd8cb6e9bb29335edbc341cac7b9"]
```
## 查询账户余额
`eth.getBalance(eth.accounts[0])`
```bash
> eth.getBalance(eth.accounts[0])
0
```
## 挖矿
`miner.start()`
{% img /image/eth_minerstart.png "eth_minerstart" %}

## 停止挖矿
`miner.stop()`
## 解锁账户
`personal.unlockAccount(eth.accounts[0])`
```bash
> personal.unlockAccount(eth.accounts[0])
Unlock account 0x12a97a88c217890aea39918b989b7ae1027295ad
Passphrase:
true
```
## 转账
`eth.sendTransaction({from:eth.accounts[0], to:eth.accounts[1], value:1000})`
{% img /image/eth_sendtrans.png "eth_sendtrans" %}

## 查询交易状态
```bash
> txpool.status
{
	pending: 1,
	queued: 0
}
```
其中有一条pending的交易，pending表示已提交但还未被处理的交易。
## 处理交易
要使交易被处理，必须要挖矿。这里我们启动挖矿，然后等待挖到一个区块之后就停止挖矿：
`miner.start(1);admin.sleepBlocks(1);miner.stop();`
{% img /image/eth_handletrans.png "eth_handletrans %}

## 获取区块高度
```bash
> eth.blockNumber
58
```
## 查看区块
`eth.getBlock(57)`
{% img /image/eth_getblock.png "eth_getblock %}

## 代币转账
```bash
eth.sendTransaction({from:eth.accounts[0], to:"0xbF584dd2094c0807d5FAb99c77c6b9cC7423Ef14", value:0, data:"0xa9059cbb000000000000000000000000bf584dd2094c0807d5fab99c77c6b9cc7423ef1400000000000000000000000000000000000000000000000ad78ebc5ac6200000"})
```
## 运行JS控制台
`geth attach ipc:\\.\pipe\geth.ipc`
# 部署合约
## 已存在的合约
获取合约内容：[https://etherscan.io/address/0x8e870d67f660d95d5be530380d0ec0bd388289e1#](https://etherscan.io/address/0x8e870d67f660d95d5be530380d0ec0bd388289e1#)
{% img /image/eth_contract.png "eth_contract %}

{% img /image/eth_contract2.png "eth_contract2 %}

```bash
abi=[{"constant":false,"inputs":[{"name":"newImplementation","type":"address"}],"name":"upgradeTo","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"newImplementation","type":"address"},{"name":"data","type":"bytes"}],"name":"upgradeToAndCall","outputs":[],"payable":true,"stateMutability":"payable","type":"function"},{"constant":true,"inputs":[],"name":"implementation","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"newAdmin","type":"address"}],"name":"changeAdmin","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"admin","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"inputs":[{"name":"_implementation","type":"address"}],"payable":false,"stateMutability":"nonpayable","type":"constructor"},{"payable":true,"stateMutability":"payable","type":"fallback"},{"anonymous":false,"inputs":[{"indexed":false,"name":"previousAdmin","type":"address"},{"indexed":false,"name":"newAdmin","type":"address"}],"name":"AdminChanged","type":"event"},{"anonymous":false,"inputs":[{"indexed":false,"name":"implementation","type":"address"}],"name":"Upgraded","type":"event"}]
```

```bash
code="0x608060405234801561001057600080fd5b5060405160208061080d83398101604081815291517f6f72672e7a657070656c696e6f732e70726f78792e696d706c656d656e74617482527f696f6e00000000000000000000000000000000000000000000000000000000006020830152915190819003602301902081906000805160206107ed8339815191521461009157fe5b6100a381640100000000610104810204565b50604080517f6f72672e7a657070656c696e6f732e70726f78792e61646d696e0000000000008152905190819003601a0190206000805160206107cd833981519152146100ec57fe5b6100fe336401000000006101c2810204565b506101dc565b600061011c826401000000006105ae6101d482021704565b15156101af57604080517f08c379a000000000000000000000000000000000000000000000000000000000815260206004820152603b60248201527f43616e6e6f742073657420612070726f787920696d706c656d656e746174696f60448201527f6e20746f2061206e6f6e2d636f6e747261637420616464726573730000000000606482015290519081900360840190fd5b506000805160206107ed83398151915255565b6000805160206107cd83398151915255565b6000903b1190565b6105e2806101eb6000396000f30060806040526004361061006c5763ffffffff7c01000000000000000000000000000000000000000000000000000000006000350416633659cfe681146100765780634f1ef286146100975780635c60da1b146100b75780638f283970146100e8578063f851a44014610109575b61007461011e565b005b34801561008257600080fd5b50610074600160a060020a0360043516610138565b61007460048035600160a060020a03169060248035908101910135610172565b3480156100c357600080fd5b506100cc6101ea565b60408051600160a060020a039092168252519081900360200190f35b3480156100f457600080fd5b50610074600160a060020a0360043516610227565b34801561011557600080fd5b506100cc610339565b610126610364565b610136610131610411565b610436565b565b61014061045a565b600160a060020a031633600160a060020a03161415610167576101628161047f565b61016f565b61016f61011e565b50565b61017a61045a565b600160a060020a031633600160a060020a031614156101dd5761019c8361047f565b30600160a060020a03163483836040518083838082843782019150509250505060006040518083038185875af19250505015156101d857600080fd5b6101e5565b6101e561011e565b505050565b60006101f461045a565b600160a060020a031633600160a060020a0316141561021c57610215610411565b9050610224565b61022461011e565b90565b61022f61045a565b600160a060020a031633600160a060020a0316141561016757600160a060020a03811615156102e557604080517f08c379a000000000000000000000000000000000000000000000000000000000815260206004820152603660248201527f43616e6e6f74206368616e6765207468652061646d696e206f6620612070726f60448201527f787920746f20746865207a65726f206164647265737300000000000000000000606482015290519081900360840190fd5b7f7e644d79422f17c01e4894b5f4f588d331ebfa28653d42ae832dc59e38c9798f61030e61045a565b60408051600160a060020a03928316815291841660208301528051918290030190a1610162816104c7565b600061034361045a565b600160a060020a031633600160a060020a0316141561021c5761021561045a565b61036c61045a565b600160a060020a031633141561040957604080517f08c379a000000000000000000000000000000000000000000000000000000000815260206004820152603260248201527f43616e6e6f742063616c6c2066616c6c6261636b2066756e6374696f6e20667260448201527f6f6d207468652070726f78792061646d696e0000000000000000000000000000606482015290519081900360840190fd5b610136610136565b7f7050c9e0f4ca769c69bd3a8ef740bc37934f8e2c036e5a723fd8ee048ed3f8c35490565b3660008037600080366000845af43d6000803e808015610455573d6000f35b3d6000fd5b7f10d6a54a4754c8869d6886b5f5d7fbfa5b4522237ea5c60d11bc4e7a1ff9390b5490565b610488816104eb565b60408051600160a060020a038316815290517fbc7cd75a20ee27fd9adebab32041f755214dbc6bffa90cc0225b39da2e5c2d3b9181900360200190a150565b7f10d6a54a4754c8869d6886b5f5d7fbfa5b4522237ea5c60d11bc4e7a1ff9390b55565b60006104f6826105ae565b151561058957604080517f08c379a000000000000000000000000000000000000000000000000000000000815260206004820152603b60248201527f43616e6e6f742073657420612070726f787920696d706c656d656e746174696f60448201527f6e20746f2061206e6f6e2d636f6e747261637420616464726573730000000000606482015290519081900360840190fd5b507f7050c9e0f4ca769c69bd3a8ef740bc37934f8e2c036e5a723fd8ee048ed3f8c355565b6000903b11905600a165627a7a72305820e6a0e94ea36527366a6a00202c99a28a36e0c44d2d0594a734640045e7169670002910d6a54a4754c8869d6886b5f5d7fbfa5b4522237ea5c60d11bc4e7a1ff9390b7050c9e0f4ca769c69bd3a8ef740bc37934f8e2c036e5a723fd8ee048ed3f8c30000000000000000000000006ffcb0f00c3ad2575e443152d8861aec1bda9ce6"
```

```bash
myContract=eth.contract(abi)

personal.unlockAccount(eth.accounts[0])

contract=myContract.new({from:eth.accounts[0],data:code,gas:1000000})

> contract
{
  abi: [{
      constant: false,
      inputs: [{...}],
      name: "upgradeTo",
      outputs: [],
      payable: false,
      stateMutability: "nonpayable",
      type: "function"
  }, {
      constant: false,
      inputs: [{...}, {...}],
      name: "upgradeToAndCall",
      outputs: [],
      payable: true,
      stateMutability: "payable",
      type: "function"
  }, {
      constant: true,
      inputs: [],
      name: "implementation",
      outputs: [{...}],
      payable: false,
      stateMutability: "view",
      type: "function"
  }, {
      constant: false,
      inputs: [{...}],
      name: "changeAdmin",
      outputs: [],
      payable: false,
      stateMutability: "nonpayable",
      type: "function"
  }, {
      constant: true,
      inputs: [],
      name: "admin",
      outputs: [{...}],
      payable: false,
      stateMutability: "view",
      type: "function"
  }, {
      inputs: [{...}],
      payable: false,
      stateMutability: "nonpayable",
      type: "constructor"
  }, {
      payable: true,
      stateMutability: "payable",
      type: "fallback"
  }, {
      anonymous: false,
      inputs: [{...}, {...}],
      name: "AdminChanged",
      type: "event"
  }, {
      anonymous: false,
      inputs: [{...}],
      name: "Upgraded",
      type: "event"
  }],
  address: undefined,
  transactionHash: "0xc3fa3a24b39a116717e89f9c3a44fc69a4b620f1a352173580baa759b661e51d"
}
查交易：
{
	"jsonrpc": "2.0", 
	"id":1, 
	"method": "eth_getTransactionReceipt", 
	"params": ["0xc3fa3a24b39a116717e89f9c3a44fc69a4b620f1a352173580baa759b661e51d"] 
}
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "blockHash": "0x9de2bcfaa9c24789470746f4f4f50404695142cf1c207757b941edbebd3f19c3",
        "blockNumber": "0x55d",
        "contractAddress": "0xc5c2f2302562498c5d94dc50fd9d4aaaa97a9932",
        "cumulativeGasUsed": "0xf4240",
        "from": "0x12a97a88c217890aea39918b989b7ae1027295ad",
        "gasUsed": "0xf4240",
        "logs": [],
        "logsBloom": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
        "root": "0x809324d522480ba4ce3aa54b10465255e4919294b81a5605c3c2023e2f87de8e",
        "to": null,
        "transactionHash": "0xc3fa3a24b39a116717e89f9c3a44fc69a4b620f1a352173580baa759b661e51d",
        "transactionIndex": "0x0"
    }
}
```
无法调用合约时：contract = web3.eth.contract(abi).at("0xc5c2f2302562498c5d94dc50fd9d4aaaa97a9932")
contract.方法名称()
# 编译合约（以PAX为例）
## 下载合约源码
[https://github.com/paxosglobal/pax-contracts](https://github.com/paxosglobal/pax-contracts)
## 安装环境
### node.js
下载：[https://nodejs.org/zh-cn/](https://nodejs.org/zh-cn/)
查看版本：node -v
### truffle
`npm install -g truffle`
`npm install truffle-hdwallet-provider`
## 编译
### 配置
 - 将truffle.js重命名为truffle-config.js
### truffle compile
出现如下报错
{% img /image/eth_contract_compile.png "eth_contract_compile %}
将D:\Tool\ETH\pax-contracts-master\contracts下的所有sol文件的pragma solidity ^0.4.28修改为pragma solidity >=0.4.21 <0.6.0;
出现如下报错

{% img /image/eth_contract_compile2.png "eth_contract_compile2 %}

将D:\Tool\ETH\pax-contracts-master\contracts下的所有sol文件的pragma experimental "v0.5.0"注释
删除文件AdminUpgradeabilityProxy.sol及相关内容

{% img /image/eth_contract_compile3.png "eth_contract_compile3 %}

{% img /image/eth_contract_compile4.png "eth_contract_compile4 %}

{% img /image/eth_contract_compile5.png "eth_contract_compile5 %}

{% img /image/eth_contract_compile6.png "eth_contract_compile6 %}

{% img /image/eth_contract_compile7.png "eth_contract_compile7 %}


## 部署
启动geth需要开启rpc，需要挖矿，需要解锁账户。
{% img /image/eth_migrate.png "eth_migrate %}
部署truffle migrate，执行命令后，会输出“Deploying Migrations...”，表示正在进行部署。

{% img /image/eth_migrate2.png "eth_migrate2 %}

查看合约地址

```bash
0x79d1b1ab89e1a7bca51016e7c26410d147f8011dffa1711e362ffdaa89a88321
{
	"jsonrpc": "2.0", 
	"id":1, 
	"method": "eth_getTransactionReceipt", 
	"params": ["0x79d1b1ab89e1a7bca51016e7c26410d147f8011dffa1711e362ffdaa89a88321"] 
}
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "blockHash": "0x8b54f351e61ed4343245c026e667fef9ec4bcc5176b5939d9ed5defd2f53f6fa",
        "blockNumber": "0x8f",
        "contractAddress": "0xc93a31c34c9517b8eb24173c9448a94a4f94c22f",
        "cumulativeGasUsed": "0x44872",
        "from": "0xbf584dd2094c0807d5fab99c77c6b9cc7423ef14",
        "gasUsed": "0x22439",
        "logs": [],
        "logsBloom": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
        "root": "0x706ea99b03604cad4b0ebe9b1fc9aec4bfede7b8ba3f826e9baf77c4cf70f854",
        "to": null,
        "transactionHash": "0x79d1b1ab89e1a7bca51016e7c26410d147f8011dffa1711e362ffdaa89a88321",
        "transactionIndex": "0x1"
    }
}
```
{% img /image/eth_migrate3.png "eth_migrate3 %}

```bash
Deploying 'Migrations'
   ----------------------
   > block number:        145
   > block timestamp:     1562742148
   > account:             0xbF584dd2094c0807d5FAb99c77c6b9cC7423Ef14
   > balance:             714.999999999999858655
   > gas used:            140345
   > gas price:           0.000000001 gwei
   > value sent:          0 ETH
   > total cost:          0.000000000000140345 ETH

   -------------------------------------
   > Total cost:     0.000000000000140345 ETH
```