---
title: ETH节点命令
---

# ETH Config
## geth节点启动命令
进入启动程序目录：`/本地源码目录/go-ethereum/build/bin`
## 启动节点命令
### 主网
```bash
./geth --identity "eth" --datadir "/本地源码目录/data"  --rpc --rpcapi "db,net,web3,eth,personal,miner" --rpccorsdomain "*"  --rpcaddr 0.0.0.0 --rpcport 8545 --networkid 1 console 2>> eth.log
```
### 测试网
```bash
./geth --testnet --datadir "/本地源码目录/testdata"  --rpc --rpcapi "db,net,web3,eth,personal,miner" --rpccorsdomain "*"  --rpcaddr 0.0.0.0 --rpcport 8545 --networkid 3 console 2>> eth.log
```
`--testnet表示选择的是ropsten测试网络，如果想要同步rinkeby测试网络，可以更换参数--testnet为--rinkeby`

| 参数名称      | 参数描述                                                     |
| ------------- | ------------------------------------------------------------ |
| identity      | 区块链的标示，用于标示目前网络的名字                         |
| nodiscover    | 私有链地址，不会被网上看到                                   |
| datadir       | 设置当前区块链网络数据存放的位置                             |
| rpc           | 开启rpc通道                                                  |
| rpcapi        | 要开放哪些rpc api，默认eth,net,web3                          |
| rpccorsdomain | 允许能连接到你的节点执行rpc api的url，使用逗号分隔。*表示任何url都可以连接 |
| rpcaddr       | HTTP-RPC服务器接口地址，默认为localhost                      |
| rpcport       | HTTP-RPC服务器端口地址，默认为8545                           |
| networkid     | 网络标识，私有链取一个大于4的随意的值                        |
| console       | 启动命令行模式，可以在Geth中执行命令                         |
| verbosity     | 日志详细度:0=silent, 1=error, 2=warn, 3=info, 4=debug, 5=detail(default: 3)<br/>2>> eth.log 表示把控制台日志输出到eth.log文件。新开一个命令行终端，运行tail -f geth.log命令来实时显示日志，这样日志信息就不会出现在终端中，让你在终端运行命令的时候可读性较差 |

`Geth uses port 30303 for connections to peers.`

## 以太坊常用networkid
| ID                           | NAME         |
| ---------------------------- | ------------ |
| 1                            | main network |
| 3                            | Ropsten      |
| 4                            | Rinkeby      |
| 42                           | Kovan        |
| 其它数字为个人自定义私有网络 |              |

0: Olympic, Ethereum public pre-release PoW testnet
1: Frontier, Homestead, Metropolis, the Ethereum public PoW main network
1: Classic, the (un)forked public Ethereum Classic PoW main network, chain ID 61
1: Expanse, an alternative Ethereum implementation, chain ID 2
2: Morden Classic, the public Ethereum Classic PoW testnet(deprecated)
3: Ropsten, the public cross-client Ethereum PoW testnet
4: Rinkeby, the public Geth-only PoA testnet
5: Goerli, the public cross-client PoA testnet
6: Kotti Classic, the public cross-client PoA testnet for Classic
8: Ubiq, the public Gubiq main network with flux difficulty chain ID 8
42: Kovan, the public Parity-only PoA testnet
60: GoChain, the GoChain networks mainnet
77: Sokol, the public POA Network testnet
99: Core, the public POA Network main network
100: xDai, the public MakerDAO/POA Network main network
31337: GoChain testnet, the GoChain networks public testnet
401697: Tobalaba, the public Energy Web Foundation testnet
7762959: Musicoin, the music blockchain
61717561: Aquachain, ASIC resistant chain
[Other]: Could indicate that your connected to a local development test network.
## 进入控制台
`./geth attach ipc:/本地数据目录/geth.ipc`
## erc20 智能合约各方法对应的签名编码
 - 常见例如：transfer(address,uint256)
编码为：web3.sha3("transfer(address,uint256)").substring(0,10)  -> "0xa9059cbb"
 - balanceOf(address)
编码为：web3.sha3("balanceOf(address)").substring(0,10)  -> "0x70a08231"

| 方法                                  | 编码       |
| ------------------------------------- | ---------- |
| transfer(address,uint256)             | 0xa9059cbb |
| balanceOf(address)                    | 0x70a08231 |
| decimals()                            | 0x313ce567 |
| allowance(address,address)            | 0xdd62ed3e |
| symbol()                              | 0x95d89b41 |
| totalSupply()                         | 0x18160ddd |
| name()                                | 0x06fdde03 |
| approve(address,uint256)              | 0x095ea7b3 |
| transferFrom(address,address,uint256) | 0x23b872dd |

## erc20 交易
传输数据：

```bash
Function: transfer(address _to, uint256 _value)

0xa9059cbb0000000000000000000000000797350000000000000000000000000000000000000000000005150ac4c39a6f3f0000
```

将数据分解为适当的块：

```bash
0x                                                     // Hex prefix
a9059cbb                                               // web3.sha3("transfer(address,uint256)").substring(0,10) => "0xa9059cbb"
0000000000000000000000000797350000000000               // address - 40 characters
000000000000000000000000000000000005150ac4c39a6f3f0000 // value - 54 characters - should be 64 characters
 
// Ruler below 
1234567890123456789012345678901234567890123456789012345678901234
```

值字段将用零填充以组成64个字符：

```bash
// Intended transfer amount
// new BigNumber("5150ac4c39a6f3f0000",16).div(1e18)
// 23999.99
000000000000000000000000000000000005150ac4c39a6f3f0000
 
// Actual transfer instruction
// new BigNumber("5150ac4c39a6f3f00000000000000",16).div(1e18)
26388268071507722.24
000000000000000000000000000000000005150ac4c39a6f3f00000000000000
 
// Ruler below 
1234567890123456789012345678901234567890123456789012345678901234
```