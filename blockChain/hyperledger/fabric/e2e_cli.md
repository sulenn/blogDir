# e2e_cli

**参考**：`Hyperledger fabric 技术内幕架构设计与实现原理`

执行 `./network_setup up`

## 1. 生成系统初始化启动的相关配置文件

调用 `generateArtifacts.sh` 脚本，该脚本调用三个函数 `genarateCerts()`、`replacePrivateKey()` 和 `generateChannelArtifacts()`。接着，使用 `docker-compose` 工具执行指定的 `docker-compose-cli.yaml` 文件，负责启动 `Faric` 网络。

### 1.1 组织成员关系与身份证书等相关文件

#### 1.1.1 genarateCerts()

- 编译生成 `cryptogen` 工具

- 使用 `cryptogen` 工具，根据 `crypto-config.yaml` 生成网络成员组织结构和对应的身份证书、签名私钥等文件。保存在 `crypto-config` 目录下

#### 1.1.2 replacePrivateKey()

读取并替换正确的私钥文件名称。

- 基于 `docker-compose-e2e-template.yaml` 模板文件创建新的配置文件 `docker-compose-e2e.yaml`

- 替换 `docker-compose-e2e.yaml` 中 `CA1_PRIVATE_KEY1` 为 `crypto-config/peerOrganization/org1.example.com/ca/` 文件名

### 1.2 节点与通道配置文件 - generateChannelArtifacts()

使用 `configtxgen` 工具基于 `configtx.yaml` 创建节点与通道配置文件，包括 `Orderer` 系统通道的创世区块文件 `genesis.block`、新建应用通道的配置交易文件 `channel.tx`、锚节点配置更新交易文件 `Org1MSPanchors.tx` 与 `Org2MSPanchors.tx` 等

---

## 2. 启动 Orderer 服务节点

`Orderer` 节点继承了 `base/dockero-compose-base.yaml` 中的 `orderer.example.com` 配置属性。`Orderer` 节点容器启动时执行 `orderer` 指令，并基于创世区块文件 `orderer.genesis.block` 创建系统通道

---

## 3. 启动 Peer 节点

节点容器启动时默认执行 `peer node start` 命令启动 `Peer` 节点。

`CLI` 命令行客户端 `cli` 启动时执行如下 `script.sh` 脚本命令。脚本执行默认的测试流程，包括创建新的应用通道、添加节点、更新锚节点配置等操作，接着执行安装链码、实例化（部署）链码、调用链码、查询链码等链码操作。

---

## 4. 创建、加入通道与更新组织锚节点配置

Hyperledger Fabric 要求创建、加入与更新通道的权限必须是具有通道组织（Org1 和 Org2）的管理员身份

### 4.1 创建新的应用通道

`script.sh` 脚本调用 `createChannel()` 函数创建新的应用通道。采用 `Org1` 管理员身份将该通道的配置交易文件 `channel.tx` 发送到 `Orderer` 排序节点，创建名称为 `mychannel` 的应用通道，并接收新建应用通道的创世区块，然后写入本地文件 `mychannel.block`

### 4.2 Peer 节点加入应用通道

调用 `jointChannel()` 函数，循环遍历所有节点并添加到应用通道中

### 4.3 更新应用通道上组织的锚节点配置

连续两次调用 `updateAnchorPeers()` 函数，更新两个组织的锚节点配置

---

## 5. 安装、实例化与调用链码

### 5.1 安装链码

连续两次调用 `installChaincode()` 函数。分别在 `Peer0/Org1` 节点与 `Peer/Org2` 节点上安装 `chaincode_example02` 链码，并将该链码命名为 `mycc` 且版本为 1.0

### 5.2 实例化（部署）链码

调用 `instantiateChaincode()`函数实例化（部署）用户链码

### 5.3 调用链码

脚本在 `Peer/Org1` 节点上继续调用链码 `chaincodeQuery()`函数与`chaincodeInvoke()`函数，启动链码容器并请求查询链码与调用链码 `mycc`

### 5.4 查询链码信息

调用 `chaincodeQuery()` 函数查询链码

---
