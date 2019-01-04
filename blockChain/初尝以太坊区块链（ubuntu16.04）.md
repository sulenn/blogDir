### 环境搭建
以太坊环境是用go语言开发，虚拟机内部运行的是javascript
使用 Personal Package Archives 直接安装，命令如下：
```
sudo apt-get install software-properties-common
sudo add-apt-repository -y ppa:ethereum/ethereum
sudo apt-get update
sudo apt-get install ethereum
```
### 区块链搭建
###### 新建文件夹，存放区块链数据。

文件夹里面新建一个叫genesis.json的配置文件，以创建自己的创世块。
配置文件的内容：
```
{
  "config": {
        "chainId": 10,
        "homesteadBlock": 0,
        "eip155Block": 0,
        "eip158Block": 0
    },
  "coinbase"   : "0x0000000000000000000000000000000000000000",
  "difficulty" : "0x020000",
  "extraData"  : "",
  "gasLimit"   : "0x2fefd8",
  "nonce"      : "0x0000000000000042",
  "mixhash"    : "0x0000000000000000000000000000000000000000000000000000000000000000",
  "parentHash" : "0x0000000000000000000000000000000000000000000000000000000000000000",
  "timestamp"  : "0x00",
  "alloc"      : {}
}
```
###### 初始化，创建创世区块
`cd 你的文件夹`
`geth --datadir data init genesis.json`
此处，--datadir是保存数据的目录，我们放在当前文件夹下一个叫data的目录下；init 是初始化的配置。

###### 启动节点
`geth --datadir data --networkid 1108 console`
此处，--networkid是表示此链的网络id为1108，以太坊的公网id是1；console表示进入js的控制台。

###### 此时应当进入js的控制台中。
以下是基本命令
查看账户

> eth.accounts
[]

创建账户

> personal.newAccount()

查看账户余额
> eth.getBalance(eth.accounts[0])
0

开始挖矿
> miner.start()

停止挖矿
> miner.stop()

第一次挖矿要等待一段时间，直至DAG文件生成完毕。。。
### 引用：
https://www.cnblogs.com/zafu/p/7921024.html
https://blog.csdn.net/u011494083/article/details/79654096
