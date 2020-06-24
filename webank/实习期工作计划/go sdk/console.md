# 控制台

   [Go SDK](https://github.com/FISCO-BCOS/go-sdk) 通过 `console.go` 实现了一个简单的控制台，支持区块链、节点信息查询和账户管理操作等。

> 注解
>
> - **Go SDK当前为候选版本，可供开发测试使用，企业级应用可用** [Web3SDK](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/sdk/java_sdk.html)
> - 安装Java版本控制台可参考 [这里](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/installation.html)

## 帮助

```shell
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
  getBlockByHash                                                 [blockHash]   [true/false]                    Query the block by its hash
  getBlockByNumber                                           [blockNumber] [true/false]               Query the block by its number
  getBlockHashByNumber                                [blockNumber]                                        Query the block hash by its number
  getBlockNumber                                                                                                                       Get the latest block height of the blockchain
  getClientVersion                                                                                                                        Get the blockchain version
  getCode                                                                  [contract address]                                  Get the contract data from contract address
  getConsensusStatus                                                                                                                 Get consensus status of nodes
  getGroupID                                                                                                                                   Get the group ID of the client
  getGroupList                                                                                                                                Get ID list of groups that the node belongs
  getGroupPeers                                                                                                                            Get all peers' ID within the group
  getNodeIDList                                                                                                                              Get ID list of nodes
  getObserverList                                                                                                                           Get the observers' ID list
  getPbftView                                                                                                                                   Get the latest PBFT view(PBFT consensus only)
  getPeers                                                                                                                                          Get the connected peers' information
  getPendingTransactions                                                                                                          Get the pending transactions
  getPendingTxSize                                                                                                                        Get the count of pending transactions
  getSealerList                                                                                                                                  Get the sealers' ID list
  getSyncStatus                                                                                                                               Get synchronization status of nodes
  getSystemConfigByKey                                     [tx_count_limit/tx_gas_limit]           Get the system configuration through key-value
  getTotalTransactionCount                                                                                                       Get the totoal transactions and the latest block height
  getTransactionByBlockHashAndIndex       [blockHash]        [transactionIndex] Query the transaction by block hash and transaction index
  getTransactionByBlockNumberAndIndex [blockNumber] [transactionIndex] Query the transaction by block number and transaction index
  getTransactionByHash                                       [transactionHash]                                   Query the transaction by its hash
  getTransactionReceipt                                       [transactionHash]                                   Query the transaction receipt by transaction hash
  help                                                                                                                                                     Help about any command
  setSystemConfigByKey                                      [tx_count_limit/tx_gas_limit]            Set the system configuration through key-value

Flags:
             --config string   config file (default is the project directory ./config.toml)
  -h,      --help            help for console
  -t,       --toggle          Help message for toggle
```

## 常用命令

### 

