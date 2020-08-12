[toc]

# go-sdk 0.10.0 提测文档

0.10.0 在上一个版本的基础上，针对预编译合约新增 API 接口和命令行，包括 permission、consensus、chainGovernance、contractLifeCycle 等。同时完成了对 AMOP 功能的支持。

**环境**：可参考 - [环境和配置文件](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/sdk/go_sdk/env_conf.html)

- Golang >= 1.13.6
- 项目采用 go module 进行包管理

**提测代码分支**：[go sdk 0.10.0](https://github.com/FISCO-BCOS/go-sdk/tree/release-0.10.0)

**测试目标**：

- 命令行测试：测试 go-sdk 提供的命令行支持，需要覆盖所有的命令
- 开发样例测试：使用 go-sdk 部署和调用合约
- AMOP测试：测试 go-sdk 支持的链上信使协议案例

**参考资料**：[控制台](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/sdk/go_sdk/console.html)

## 命令行功能测试

**编译命令行工具**：go-sdk 主目录执行 *go build cmd/console.go*，获取 console 工具

- 可以使用 web3 控制台配合测试，比如部署合约、产生区块信息、交易信息等
- 测试时可以注意输出、错误提示等是否合理，易于阅读
- 进行边界测试、异常测试、鲁棒性测试，查看是否会出现异常输出
- 兼容性测试，测试 2.4.0、2.5.0、2.6.0
- 国密、非国密。go-sdk 的默认配置是非国密，切换成国密需要将 SMCrypto 配置项修改为 true，将 KeyFile 配置项修改为国密账号（配置文件配置项旁边注释的那一行就是国密账号）

### 常规命令

**命令包括**：getBlockByHash、getBlockByNumber、getBlockHashByNumber、getBlockNumber、getClientVersion、getCode、getConsensusStatus、getGroupID、getGroupList、getGroupPeers、getNodeIDList、getObserverList、getPbftView、getPeers、getPendingTransactions、getPendingTxSize、getSealerList、getSyncStatus、getSystemConfigByKey、getTotalTransactionCount、getTransactionByBlockHashAndIndex、getTransactionByBlockNumberAndIndex、getTransactionByHash、getTransactionReceipt

- getBlockByHash：可以先使用 web3 sdk 控制台获取创世区块的 hash 值，如 *getBlockHashByNumber 0*，
- getCode：需要用到合约地址，可以先使用 web3 sdk 控制台部署一个合约，如 *deploy HelloWorld*，参考：[deploy](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/manual/console.html#deploy)
- getSystemConfigByKey：支持 tx_count_limit、tx_gas_limit、rpbft_epoch_sealer_num、rpbft_epoch_block_num 四个配置项
- getTransactionByBlockHashAndIndex：注意索引需要是 16 进制
- getTransactionByBlockNumberAndIndex：注意索引需要是 16 进制
- getTransactionReceipt：参数为交易 hash 值，可以使用 web3 sdk 控制台命令获得，如 *getBlockHashByNumber 0*，

### SystemConfigService

**命令包括**：setSystemConfigByKey

- setSystemConfigByKey：支持 tx_count_limit、tx_gas_limit、rpbft_epoch_sealer_num、rpbft_epoch_block_num 四个配置项

### PermissionService

**命令包括**：grantUserTableManager、revokeUserTableManager、listUserTableManager、grantDeployAndCreateManager、revokeDeployAndCreateManager、listDeployAndCreateManager、grantPermissionManager、revokePermissionManager、listPermissionManager、grantNodeManager、revokeNodeManager、listNodeManager、grantCNSManager、revokeCNSManager、listCNSManager、grantSysConfigManager、revokeSysConfigManager、listSysConfigManager

- grantUserTableManager、revokeUserTableManager：这两个命令需要先使用 web3控制台部署用户表，部署用户表可使用 web3sdk 控制台，参考：[create sql](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/manual/console.html#create-sql)。外部用户账号可以使用 web3 控制台中的 get_account.sh 脚本生成，参考：[使用脚本生成PEM格式私钥](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/manual/account.html#pem)

- grantPermissionManager BCOS 2.5 即以上版本被禁用
- revokePermissionManager BCOS 2.5 即以上版本被禁用

### CNSService

**命令包括**：queryCNS、getAddressByContractNameAndVersion

需要先使用 CNS 部署合约，部署方式可使用 web3sdk 控制台，参考：[deployByCNS](https://fisco-bcos-doc-qiubing.readthedocs.io/en/latest/docs/manual/console.html#deploybycns)

- queryCNS：参数1 - 合约名（必选），参数2 - 版本号（可选）

### ConsensusService

**命令包括**：addObserver、addSealer、removeNode

*注意不要对 node0 节点进行操作*

### ChainGovernanceService

grantCommitteeMember、revokeCommitteeMember、listCommitteeMembers、queryCommitteeMemberWeight、updateCommitteeMemberWeight、queryThreshold、updateThreshold、grantOperator、revokeOperator、listOperators、freezeAccount、unfreezeAccount、getAccountStatus

*基于角色的权限控制比较繁琐一点，彼此的权限设置直接互相会影响*

- grantCommitteeMember：使用 *0x83309d045a19c44dc3722d15a6abd472f95866ac* 这个外部账号，因为 go-sdk 的配置文件中使用的是这个账号对应的私钥。如果不使用这个外部账号，那么开启角色控制权限后，go-sdk 默认使用的私钥将无法调用其它权限控制命令

- updateCommitteeMemberWeight：大于 0 的整数
- updateThreshold：支持范围 [0, 99)
- grantOperator：不能使用和委员会相同的外部账号，委员会不能拥有运维的权限。也就是说不能使用 *0x83309d045a19c44dc3722d15a6abd472f95866ac* 外部账号
- freezeAccount：该命令只适用于部署过合约的外部账号。获取这种外部账号类型的方式之一是指定账号启动 web3 sdk 控制台，然后在控制台中部署一个合约。指定账号启动控制台可参考：[控制台加载私钥](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/manual/console.html#id17)

### ContractLifeCycleService

freezeContract、unfreezeContract、grantContractStatusManager、getContractStatusListManager

*合约账号需要部署获得*，可参考：[deploy](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/manual/console.html#deploy)

## 合约部署和调用测试

在利用SDK进行项目开发时，对智能合约进行操作需要利用go-sdk的`abigen`工具将Solidity智能合约转换为`Go`文件代码。整体上主要包含六个流程：

- 准备需要编译的智能合约
- 配置好相应版本的solc编译器
- 构建go-sdk的合约编译工具abigen
- 编译生成go文件
- 准备建立ssl连接需要的证书
- 使用生成的go文件进行合约部署、调用

*案例详细步骤参考*：[合约开发样例](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/sdk/go_sdk/contractExamples.html#id1)

**测试目标**：

- 跑通非国密和国密案例，案例包括：HelloWorld样例、KVTableTest样例、TableTest样例
- 兼容性测试：测试 2.4.0、2.5.0、2.6.0

## amop 案例测试

AMOP（Advanced Messages Onchain Protocol）即链上信使协议，旨在为联盟链提供一个安全高效的消息信道，联盟链中的各个机构，只要部署了区块链节点，无论是共识节点还是观察节点，均可使用AMOP进行通讯

*案例详细步骤参考*：[AMOP 使用案例](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/sdk/go_sdk/amopExamples.html)

**测试目标**：

- 跑通单播和多播案例
- 测试同 java sdk 的 amop 通信
- 兼容性测试：测试 2.5.0、2.6.0
- 暂不支持 amop_auth

### 同 java sdk 的通信测试

- **单播 - go-sdk 作为消息发布者**：

  go sdk 运行：go run examples/amop/unicast_pub/publisher.go   127.0.0.1:20201 hello

  java sdk 运行：java -cp 'conf/:apps/*:lib/*' org.fisco.bcos.channel.test.amop.Channel2Server hello

- **多播 - go-sdk 作为消息发布者**：

  go sdk 运行：go run examples/amop/multicast_pub/publisher.go   127.0.0.1:20201 hello

  java sdk 运行：java -cp 'conf/:apps/*:lib/*' org.fisco.bcos.channel.test.amop.Channel2Server hello

- 同理，也可以测试 go-sdk 作为消息接受者，java sdk 作为消息发布者