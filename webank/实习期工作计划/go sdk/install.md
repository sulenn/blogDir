# 快速安装

## 环境要求

- Go开发环境

  	- Golang >= 1.13.6
   - 项目采用 go module 进行包管理。可参考 [Using Go Modules](https://blog.golang.org/using-go-modules)

  如果您没有部署过Go环境，可参考 [官方文档](https://golang.org/doc/)

- 基本开发组件

  - Git（Windows、Linux及MacOS需要）
  - Git bash（仅Windows需要）

- FISCO BCOS节点：请参考 [FISCO BCOS安装](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/installation.html#fisco-bcos) 搭建

> 推荐使用 ubuntu 18.04 进行开发

## 部署Go SDK

1. **拉取源代码**

```shell
git clone https://github.com/FISCO-BCOS/go-sdk.git
```

2. **安装依赖项**

进入 go-sdk 目录，执行命令：

```shell
go mod download
# 如果依赖下载缓慢，可以设置开源代理（需使用 go module 特性）
export GOPROXY=https://goproxy.io
```

## Go SDK控制台

Go SDK 提供控制台工具，供用户在命令行中方便地与区块链进行交互。控制台工具在 Go SDK 提供的 API 基础上开发而成，使用方式与结果输出对脚本友好。

### 快速建链

```shell
# 获取开发部署工具
curl -LO https://github.com/FISCO-BCOS/FISCO-BCOS/releases/download/`curl -s https://api.github.com/repos/FISCO-BCOS/FISCO-BCOS/releases | grep "\"v2\.[0-9]\.[0-9]\"" | sort -u | tail -n 1 | cut -d \" -f 4`/build_chain.sh && chmod u+x build_chain.sh
# 在本地建一个4节点的FISCO BCOS链
bash build_chain.sh -l "127.0.0.1:4" -p 30300,20200,8545 -i
# 启动FISCO BCOS链
bash nodes/127.0.0.1/start_all.sh
```

### 配置证书

修改配置文件，证书配置位于 `./go-sdk/config.toml` 文件的 `Network` 配置项中。需要根据实际使用的证书文件的路径修改该配置项的 `Key`、`Cert` 及 `CAFile` 配置，其中 `Key` 为 SDK 私钥文件的路径，`Cert` 为 SDK 证书文件的路径，`CAFile` 为链根证书文件的路径，这些文件可以由 [开发部署工具](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/manual/build_chain.html) 或 [运维部署工具](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/enterprise_tools/index.html) 自动生成，具体的生成方式及文件位置请参阅上述工具的说明文档。

### 控制台使用

- 编译控制台工具

```shell
# 在 go-sdk 目录执行指令
go build cmd/console.go
```

- 使用控制台工具

```shell
# 查看帮助
> ./console -h
console is a Golang client for FISCO BCOS 2.0.0 and it supports the JSON-RPC 
service and the contract operations(e.g. deploying && writing contracts).

Also, console can be used as a Go package for FISCO BCOS that just simply adding 
the import statement:

    import "github.com/FISCO-BCOS/go-sdk" 
or 
    import "github.com/FISCO-BCOS/go-sdk/client" # using the client package

Please access the github site for more details:
    https://github.com/FISCO-BCOS/go-sdk.

Usage:
  console [command]

Available Commands:
  getBlockByHash                      [blockHash]   [true/false]       Query the block by its hash
  getBlockByNumber                    [blockNumber] [true/false]       Query the block by its number
  getBlockHashByNumber                [blockNumber]                    Query the block hash by its number
  getBlockNumber                                                       Get the latest block height of the blockchain
  getClientVersion                                                     Get the blockchain version
  getCode                             [contract address]               Get the contract data from contract address
  getConsensusStatus                                                   Get consensus status of nodes
  getGroupID                                                           Get the group ID of the client
  getGroupList                                                         Get ID list of groups that the node belongs
  getGroupPeers                                                        Get all peers' ID within the group
  getNodeIDList                                                        Get ID list of nodes
  getObserverList                                                      Get the observers' ID list
  getPbftView                                                          Get the latest PBFT view(PBFT consensus only)
  getPeers                                                             Get the connected peers' information
  getPendingTransactions                                               Get the pending transactions
  getPendingTxSize                                                     Get the count of pending transactions
  getSealerList                                                        Get the sealers' ID list
  getSyncStatus                                                        Get synchronization status of nodes
  getSystemConfigByKey                [tx_count_limit/tx_gas_limit]    Get the system configuration through key-value
  getTotalTransactionCount                                             Get the totoal transactions and the latest block height
  getTransactionByBlockHashAndIndex   [blockHash]   [transactionIndex] Query the transaction by block hash and transaction index
  getTransactionByBlockNumberAndIndex [blockNumber] [transactionIndex] Query the transaction by block number and transaction index
  getTransactionByHash                [transactionHash]                Query the transaction by its hash
  getTransactionReceipt               [transactionHash]                Query the transaction receipt by transaction hash
  help                                Help about any command
  setSystemConfigByKey                [tx_count_limit/tx_gas_limit]    Set the system configuration through key-value

Flags:
      --config string   config file (default is the project directory ./config.toml)
  -h, --help            help for console
  -t, --toggle          Help message for toggle

Use "console [command] --help" for more information about a command.

# 查看区块链使用的 fisco-bcos 版本信息
> ./console getClientVersion
Client Version: 
{
  "Build Time": "20200610 15:42:05",
  "Build Type": "Linux/g++/RelWithDebInfo",
  "Chain Id": "1",
  "FISCO-BCOS Version": "2.5.0",
  "Git Branch": "master",
  "Git Commit Hash": "b0978f773ca1dbb499a4343b9fb3a12c40b8fc97",
  "Supported Version": "2.4.0"
}
```

- 开启命令行支持：

> 注解
>
> - 此步骤只需设置一次，设置之后重启终端即可使用

```shell
# bash 命令行自动补全，请在 bash 环境下执行此步骤
./console completion bash > go_sdk_completion && sudo mv go_sdk_completion /etc/bash_completion.d/
# zsh 命令行自动补全，请在 zsh 环境下执行此步骤
./console completion zsh > ~/.go-sdk-completion.sh && echo 'source ~/.go-sdk-completion.sh' >> ~/.zshrc
```

