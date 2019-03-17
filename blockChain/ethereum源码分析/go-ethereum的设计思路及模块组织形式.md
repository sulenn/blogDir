# go-ethereum的设计思路及模块组织形式

以太坊的目标是基于区块链技术打造一个运行智能合约的去中心化平台。

## 一. 区块链技术

区块链属于一种去中心化的数字记账技术，区块链数据由彼此互不信任的节点共同维护，每个节点复制一份完整的记录。

## 二. 以太坊核心概念

* EVM: 以太坊虚拟机，轻量级的虚拟机环境，是以太坊智能合约的运行环境。
* Account: 账户分为两类，合约账户和外部账户。合约账户主要存储执行的合约代码，外部账户存储以太币，对应具体共钥。
* Transaction: 以太坊网络上的交易，从一个账户到另一个账户的消息，包括以太币或这智能合约参数。
* Gas: 以太坊网络运行的燃料，每执行一条智能合约，都会消耗一定的燃料。
* Mine: 挖矿，以太坊网络通过工作量证明算法来保证网络的安全运行。
* P2P网络: 以太坊分布式网络中的所有节点都地位平等，没有中心服务器。

## 三. 以太坊模型

以太坊本质是一个基于交易的状态机(transaction-based state machine)，以太坊的状态中有百万个交易，
这些交易被打包到一个区块(Block)中，每个区块都和之前的区块链接起来，形成一个反向链表，所以叫区块链。在区块链的基础上增加了智能合约打造出以太坊。

为了让一笔交易被认为是有效的，它必须要经过一个验证过程，也就是挖矿(Mine)。 任何一个以太坊网络上的矿工都可以尝试创建和验证区块，
如果一个区块被认为是有效的，并且是最快完成验证的，那么就会添加到主链上，主链是以太坊网络上最长的一条链。如果同时有多个矿工打包了一个区块，加上区块在网络中传播需要一定时间，
难免会产生多条路径，就是所谓的分叉。
为了防止多条链的产生，以太坊使用了 **GHOST 协议** (Greedy Heaviest Observed Subtree)，也就是选择一条完成计算最多的路径，区块号越大， 路径就越长，说明挖矿消耗的算力越多。
因为成功证实了一个新区块会得到一定以太币的奖励，所以从经济学和博弈论的角度，选择主链是最优的。

## 四. 以太坊的架构

以太坊的架构设计可以简单的分为三个层次，协议层、接口层和应用层。而协议层又可以分为网络层和存储层。


从技术角度看，协议层主要包括P2P网络通信，分布式算法，加密签名和数据存储技术。数据存储底层，比特币和以太坊都选用了Google开源的LevelDB数据库。

接口层与协议层完全分离，除了交易时与协议层进行交互，保证开发各种基于区块链的应用层业务不受约束，包括分布式存储业务，机器学习，物联网等。

应用层主要是从区块链自身的特性出发，在不引用第三方机构的前提下，提供去中心化，不可篡改，安全可靠的场景应用。
主要包括金融服务，征信和权属管理，资源共享，投资管理以及物联网和供应链等。

## 五. 以太坊核心数据结构

1. 区块(Block)是以太坊的核心数据结构之一，Block包含Header和Body两部分。

2. Blockchain和HeaderChain， Blockchain管理所有的Block, 让其组成一个单向链表。Headerchain管理所有的Header,也形成一个单向链表， Headerchain是Blockchain里面的一部分。

3. Transaction是Body的重要数据结构，一个交易就是被外部拥有账户生成的加密签名的一段指令，序列化，然后提交给区块链。

4. 以太坊的数据库体系-Merkle-Patricia Trie(MPT)， 它是由一系列节点组成的二叉树，在树底包含了源数据的大量叶子节点， 父节点是两个子节点的Hash值，一直到根节点。

## 六. go-ethereum源码的目录结构

``` golang
|---accounts                以太坊账户管理
|---bmt                     二进制Merkle-Patricia Trie的实现
|---build                   主要是编译和构建的一些脚本和配置
|---cmd                     命令行工具
|     |---abigen            合约接口生成工具
|     |---bootnode          实现网络发现的节点
|     |---evm               以太坊虚拟机
|     |---faucet
|     |---geth              以太坊命令行客户端
|     |---p2psim            提供了一个工具来模拟http的API
|     |---puppeth           创建一个新的以太坊网络的向导
|     |---rlpdump           提供RLP数据的格式化输出
|     |---swarm             swarm网络的接入点
|     |---util              公共的组件
|     |---wnode             这是一个简单的Whisper节点。 它可以用作独立的引导节点。此外，可以用于不同的测试和诊断目的。
|---common                  提供了一些公共的工具类
|---consensus               以太坊的共识算法，包括ethhash, clique
|---core                    以太坊的核心数据结构和算法(虚拟机，状态，区块链，布隆过滤器)
|---crypto                  加密,数字签名和hash算法
|---eth                     实现了以太坊的协议
|---ethclient               以太坊的RPC客户端
|---ethdb                   eth的数据库,主要是LevelDB及相应接口
|---event                   实时事件处理
|---light                   实现为以太坊轻量级客户端提供按需检索的功能
|---metrics                 提供磁盘计数器
|---miner                   以太坊的挖矿和共识算法
|---mobile                  移动端使用的一些warpper
|---node                    以太坊的多种类型的节点
|---p2p                     以太坊p2p网络协议
|---rlp                     以太坊序列化和反序列化处理
|---rpc                     远程方法调用
|---swarm                   swarm网络处理
|---trie                    以太坊重要的数据结构MPT的实现
|---whisper                 whisper节点的协议
```

## 七. 资料

[以太坊官方文档](http://ethdocs.org/en/latest/index.html)

[以太坊设计原理](https://github.com/ethereum/wiki/wiki/Design-Rationale)

[以太坊白皮书](https://github.com/ethereum/wiki/wiki/White-Paper)

[以太坊黄皮书](https://ethereum.github.io/yellowpaper/paper.pdf)

## go-ethereum源码解析

因为go ethereum是最被广泛使用的以太坊客户端， 所以后续的源码分析都从github上面的这份代码进行分析。 然后我使用的是windows 10 64位的环境。

### 搭建go ethereum调试环境

首先下载go安装包进行安装，因为GO的网站被墙，所以从下面地址下载。

[https://studygolang.com/dl/golang/go1.9.1.windows-amd64.msi](https://studygolang.com/dl/golang/go1.9.1.windows-amd64.msi)

安装好之后，设置环境变量，把C:\Go\bin目录添加到你的PATH环境变量， 然后增加一个GOPATH的环境变量，GOPATH的值设置为你的GO语言下载的代码路径(我设置的是C:\GOPATH)

安装git工具，请参考网络上的教程安装git工具， go语言从github自动下载代码需要git工具的支持

打开命令行工具下载 go-ethereum的代码

`go get github.com/ethereum/go-ethereum`

命令执行成功之后，代码就会下载到下面这个目录，%GOPATH%\src\github.com\ethereum\go-ethereum
如果执行过程中出现

    github.com/ethereum/go-ethereum/crypto/secp256k1
    exec: "gcc": executable file not found in %PATH%

则需要安装gcc工具，我们从下面地址下载并安装

[http://tdm-gcc.tdragon.net/download](http://tdm-gcc.tdragon.net/download)

接下来安装IDE工具。 我是用的IDE是JetBrains的Gogland。 可以在下面地址下载

[https://download.jetbrains.com/go/gogland-173.2696.28.exe](https://download.jetbrains.com/go/gogland-173.2696.28.exe)

安装完成后打开IDE. 选择File -> Open -> 选择GOPATH\src\github.com\ethereum\go-ethereum目录打开。

然后打开go-ethereum/rlp/decode_test.go. 在编辑框右键选择运行， 如果运行成功，代表环境搭建完成。

### go ethereum 目录大概介绍

go-ethereum项目的组织结构基本上是按照功能模块划分的目录，下面简单介绍一下各个目录的结构，每个目录在GO语言里面又被成为一个Package,我理解跟Java里面的Package应该是差不多的意思。

    accounts            实现了一个高等级的以太坊账户管理
    bmt         二进制的默克尔树的实现
    build           主要是编译和构建的一些脚本和配置
    cmd         命令行工具，又分了很多的命令行工具，下面一个一个介绍
        /abigen     Source code generator to convert Ethereum contract definitions into easy to use, compile-time type-safe Go packages
        /bootnode   启动一个仅仅实现网络发现的节点
        /evm        以太坊虚拟机的开发工具， 用来提供一个可配置的，受隔离的代码调试环境
        /faucet
        /geth       以太坊命令行客户端，最重要的一个工具
        /p2psim     提供了一个工具来模拟http的API
        /puppeth    创建一个新的以太坊网络的向导
        /rlpdump    提供了一个RLP数据的格式化输出
        /swarm      swarm网络的接入点
        /util       提供了一些公共的工具
        /wnode      这是一个简单的Whisper节点。 它可以用作独立的引导节点。此外，可以用于不同的测试和诊断目的。
    common          提供了一些公共的工具类
    compression     Package rle implements the run-length encoding used for Ethereum data.
    consensus       提供了以太坊的一些共识算法，比如ethhash, clique(proof-of-authority)
    console         console类
    contracts
    core            以太坊的核心数据结构和算法(虚拟机，状态，区块链，布隆过滤器)
    crypto          加密和hash算法，
    eth         实现了以太坊的协议
    ethclient       提供了以太坊的RPC客户端
    ethdb           eth的数据库(包括实际使用的leveldb和供测试使用的内存数据库)
    ethstats        提供网络状态的报告
    event           处理实时的事件
    les         实现了以太坊的轻量级协议子集
    light           实现为以太坊轻量级客户端提供按需检索的功能
    log         提供对人机都友好的日志信息
    metrics         提供磁盘计数器
    miner           提供以太坊的区块创建和挖矿
    mobile          移动端使用的一些warpper
    node            以太坊的多种类型的节点
    p2p         以太坊p2p网络协议
    rlp         以太坊序列化处理
    rpc         远程方法调用
    swarm           swarm网络处理
    tests           测试
    trie            以太坊重要的数据结构Package trie implements Merkle Patricia Tries.
    whisper         提供了whisper节点的协议。

可以看到以太坊的代码量还是挺大的，但是粗略看，代码结构还是挺好的。我希望先从一些比较独立的模块来进行分析。然后在深入分析内部的代码。重点可能集中在黄皮书里面没有涉及到的p2p网络等模块。

**参考**：[https://blog.csdn.net/itcastcpp/article/details/80304070](https://blog.csdn.net/itcastcpp/article/details/80304070)