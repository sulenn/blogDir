[toc]

# go-sdk 0.10.0 提测文档

## 命令行自动补全功能

## 命令行功能新增

go build cmd/console.go 生成命令工具，./consle command parameters 执行。./console help 查看所有命令，建议直接通过 help 提示测试命令行功能。一是可以检查命令行编写、提示信息等是否描述不当、有错，第二是测试更直观。

### chain_governance 基于角色的权限控制

开启权限控制之后，权限功能彼此是会互相产生影响的，比如未授权的账户无法部署合约等。详细说明请参考：[基于角色的权限控制](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/manual/permission_control.html#id2)

- grantCommitteeMember：授予用户账号链治理委员会委员权限

  ```shell
  grantCommitteeMember [accountAddress]
  ```

- revokeCommitteeMember：撤销用户账号链治理委员会委员权限

  ```shell
  revokeCommitteeMember [accountAddress]
  ```

- listCommitteeMembers：列出拥有委员权限的所有用户信息

  ```shell
  listCommitteeMembers
  ```

- queryCommitteeMemberWeight：查询委员拥有的投票权重，默认为1

  ```shell
  queryCommitteeMemberWeight [accountAddress]
  ```

- updateCommitteeMemberWeight：更新委员拥有的投票权重

  ```shell
  updateCommitteeMemberWeight [accountAddress]
  ```

- updateThreshold：更新所有委员投票生效的阈值。比如所有委员当前投票能达到的最大权重和为10，而阈值为5，那么只需要投票和大于等于5，投票结果即可生效

  ```shell
  // threshold 取值范围为 [0, 100)
  updateThreshold [threshold]
  ```

- queryThreshold：查询阈值

  ```shell
  queryThreshold
  ```

- grantOperator：授予用户账户运维权限

  ```shell
  grantOperator [accountAddress]
  ```

- revokeOperator：撤销用户账户运维权限

  ```shell
  revokeOperator [accountAddress]
  ```

- listOperators：列出拥有运维权限的所有用户信息

  ```shell
  listOperators
  ```

- freezeAccount：冻结用户账户

  ```shell
  freezeAccount [accountAddress]
  ```

- unfreezeAccount：解冻用户账户

  ```shell
  unfreezeAccount [accountAddress]
  ```

- getAccountStatus：获取某一个账户的状态信息，是否可用/被冻结

  ```shell
  getAccountStatus [accountAddress]
  ```

### consensus 共识表

**参考**：[ConsensusService](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/sdk/go_sdk/api.html#consensusservice)

### contract_life_cycle 合约生命周期

- freezeContract：冻结合约

  ```shell
  freezeContract [contractAddress]
  ```

- unfreezeContract：解冻合约

  ```shell
  unfreezeContract [contractAddress]
  ```

- grantContractStatusManager：授予用户账号合约管理权限

  ```shell
  grantContractStatusManager [contractAddress] [accountAddress]
  ```

- getContractStatus：查询合约状态

  ```shell
   grantContractStatusManager [contractAddress]
  ```

- listContractStatusManager：查询拥有某合约管理权限的所有用户信息

  ```shell
  listContractStatusManager [contractAddress]
  ```

### permission 基于表的权限管理

**参考**：[PermissionService](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/sdk/go_sdk/api.html#permissionservice)

### system_config

**参考**：[SystemConfigService](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/manual/permission_control.html#id24)

其中 setSystemConfigByKey 支持四个参数：tx_count_limit、tx_gas_limit、rpbft_epoch_sealer_num、rpbft_epoch_block_num