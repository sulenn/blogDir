[toc]

# go-sdk 0.10.0 提测文档

0.10.0 在上一个版本的基础上，针对预编译合约补全、新增 API 接口和命令行，包括 permission、consensus、chainGovernance、contractLifeCycle 等。同时完成了对 AMOP 功能的支持。

**测试目标**：

1. 测试命令行，需要覆盖所有的命令
2. 测试提供的 AMOP 案例

**参考资料**：[控制台](https://fisco-bcos-doc-qiubing.readthedocs.io/en/latest/docs/sdk/go_sdk/console.html)

## 命令行功能测试

- 可以使用 web3 控制台配合测试，比如部署合约、产生区块信息、交易信息等
- 测试时可以注意输出、错误提示等是否合理，易于阅读
- 进行鲁棒性测试，查看是否会出现异常输出
- 兼容性测试，测试 2.4.0、2.5.0、2.6.0

### 常规命令

**命令包括**：getBlockByHash、getBlockByNumber、getBlockHashByNumber、getBlockNumber、getClientVersion、getCode、getConsensusStatus、getGroupID、getGroupList、getGroupPeers、getNodeIDList、getObserverList、getPbftView、getPeers、getPendingTransactions、getPendingTxSize、getSealerList、getSyncStatus、getSystemConfigByKey、getTotalTransactionCount、getTransactionByBlockHashAndIndex、getTransactionByBlockNumberAndIndex、getTransactionByHash、getTransactionReceipt

- getSystemConfigByKey：支持 tx_count_limit、tx_gas_limit、rpbft_epoch_sealer_num、rpbft_epoch_block_num 四个配置项

### SystemConfigService

**命令包括**：setSystemConfigByKey

- setSystemConfigByKey：支持 tx_count_limit、tx_gas_limit、rpbft_epoch_sealer_num、rpbft_epoch_block_num 四个配置项

### PermissionService

**命令包括**：grantUserTableManager、revokeUserTableManager、listUserTableManager、grantDeployAndCreateManager、revokeDeployAndCreateManager、listDeployAndCreateManager、grantPermissionManager、revokePermissionManager、listPermissionManager、grantNodeManager、revokeNodeManager、listNodeManager、grantCNSManager、revokeCNSManager、listCNSManager、grantSysConfigManager、revokeSysConfigManager、listSysConfigManager

- grantPermissionManager BCOS 2.5 即以上版本被禁用
- revokePermissionManager BCOS 2.5 即以上版本被禁用
- grantUserTableManager、revokeUserTableManager：这两个命令需要先使用 web3控制台部署用户表，部署用户表可使用 web3sdk 控制台，参考：[create sql](https://fisco-bcos-doc-qiubing.readthedocs.io/en/latest/docs/manual/console.html#create-sql)

### CNSService

**命令包括**：queryCNS、getAddressByContractNameAndVersion

需要先使用 CNS 部署合约，部署方式可使用 web3sdk 控制台，参考：[**deployByCNS**](https://fisco-bcos-doc-qiubing.readthedocs.io/en/latest/docs/manual/console.html#deploybycns)

### ConsensusService

**命令包括**：addObserver、addSealer、removeNode

*注意不要对把 node0 节点进行操作*

### ChainGovernanceService

grantCommitteeMember、revokeCommitteeMember、listCommitteeMembers、queryCommitteeMemberWeight、updateCommitteeMemberWeight、queryThreshold、updateThreshold、grantOperator、revokeOperator、listOperators

*基于角色的权限控制比较繁琐一点，彼此的权限设置直接互相会影响*



## ContractLifeCycleService

freezeAccount、unfreezeAccount、getAccountStatus、freezeContract、unfreezeContract、grantContractStatusManager、getContractStatusListManager

*合约账号需要部署获得*，可参考：[**deploy**](https://fisco-bcos-doc-qiubing.readthedocs.io/en/latest/docs/manual/console.html#deploy)

## amop 案例测试