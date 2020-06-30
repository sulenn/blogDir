# Go API

Go SDK为区块链应用开发者提供了Go API接口，以服务的形式供外部调用。按照功能，Go API可以分为如下几类：

- **Web3jService**：：提供访问FISCO BCOS 2.0+节点[JSON-RPC](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/api.html)接口支持、提供部署及调用合约的支持；
- **PrecompiledService**：Precompiled合约（预编译合约）是一种FISCO BCOS底层内嵌的、通过C++实现的高效智能合约，提供包括[分布式权限控制](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/design/security_control/permission_control.html)、[CNS](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/design/features/cns_contract_name_service.html)、系统属性配置、节点类型配置等功能。PrecompiledService是调用这类功能的API的统称，分为：
  - **PermissionService**：提供对分布式权限控制的支持
  - **CNSService**：提供对CNS的支持
  - **SystemConfigService**：提供对系统配置的支持
  - **ConsensusService**：提供对节点类型配置的支持
  - **CRUDService**：提供对CRUD(增删改查)操作的支持，可以创建表或对表进行增删改查操作。

## Web3jService

**位置**：go-sdk/client/goclient.go

**功能**：访问FISCO BCOS 2.0+节点[JSON-RPC](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/api.html)

| 接口名                              | 描述                                                     | 参数                                                         |
| ----------------------------------- | -------------------------------------------------------- | ------------------------------------------------------------ |
| GetClientVersion                    | 获取区块链节点版本信息                                   | 无                                                           |
| GetBlockNumber                      | 获取最新块高                                             | 无                                                           |
| GetPbftView                         | 获取PBFT视图                                             | 无                                                           |
| GetBlockLimit                       | 获取最新区块高度限制                                     | 无                                                           |
| GetSealerList                       | 获取共识节点列表                                         | 无                                                           |
| GetObserverList                     | 获取观察者节点列表                                       | 无                                                           |
| GetConsensusStatus                  | 获取区块链节点共识状态                                   | 无                                                           |
| GetSyncStatus                       | 获取区块链节点同步状态                                   | 无                                                           |
| GetPeers                            | 获取区块链节点的连接信息                                 | 无                                                           |
| GetGroupPeers                       | 获取指定群组的共识节点和观察节点列表                     | 无                                                           |
| GetNodeIDList                       | 获取节点及其连接节点的列表                               | 无                                                           |
| GetGroupList                        | 获取节点所属群组的群组ID列表                             | 无                                                           |
| GetBlockByHash                      | 根据区块哈希获取区块信息                                 | 区块哈希 & bool                                              |
| GetBlockByNumber                    | 根据区块高度获取区块信息                                 | 区块高度 & bool                                              |
| GetBlockHashByNumber                | 根据区块高度获取区块哈希                                 | 区块高度                                                     |
| GetTransactionByHash                | 根据交易哈希获取交易信息                                 | 交易哈希                                                     |
| GetTransactionByBlockHashAndIndex   | 根据交易所属的区块哈希、 交易索引获取交易信息            | 交易所属的区块哈希 & 交易索引                                |
| GetTransactionByBlockNumberAndIndex | 根据交易所属的区块高度、 交易索引获取交易信息            | 交易所属的区块高度 & 交易索引                                |
| GetTransactionReceipt               | 根据交易哈希获取交易回执                                 | 交易哈希                                                     |
| GetContractAddress                  | 根据部署合约时产生的交易地址获取合约地址                 | 交易哈希                                                     |
| GetPendingTransactions              | 获取交易池内所有未上链的交易                             | 无                                                           |
| GetPendingTxSize                    | 获取交易池内未上链的交易数目                             | 无                                                           |
| GetCode                             | 根据合约地址查询合约数据                                 | 合约地址                                                     |
| GetTotalTransactionCount            | 获取指定群组的上链交易数目                               | 无                                                           |
| GetSystemConfigByKey                | 根据关键字获取区块链系统配置                             | 系统配置关键字，目前支持：<br/>\- tx_count_limit<br/>\- tx_gas_limit |
| Call                                | 调用只读合约                                             | 合约地址<br/>调用接口*<br/>参数列表                          |
| SendRawTransaction                  | 发送一个经过签名的交易，该交易随后会被链上节点执行并共识 | 群组ID & 已签名交易                                          |

*调用接口：函数名(参数类型,…)，例如：func(uint256,uint256)，参数类型之间不能有空格

## PrecompiledService

