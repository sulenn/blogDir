[TOC]

---

####任务描述

本关任务：根据下面的相关知识，完成与 `MSP` 成员管理介绍相关的选择题。

####相关知识

通信安全是区块链技术的重要组成部分，联盟链由多个不同的组织组成，且每一个组织又可以由多个节点组成。本关主要介绍 `Hyperledger Fabric` 中 `MSP` 的相关概念以及如何使用 `MSP` 来确保通信安全。

#####`MSP` 的定义与作用

在 `Hyperledger Fabric`中，各个网络参与者之间的通信安全依赖于`PKI`（`Public Key Infrastructure`，公钥基础结构）标准实现，并确保在区块链上发布的消息得到相应的认证。

`PKI` 由向各方（如服务的用户、服务提供商）发布数字证书的颁发机构组成，参与者可以使用颁发的证书在特定的网络环境中进行身份验证。

`PKI`有 4 个关键要素：

- `数字证书`：包含与证书持有者相关的一组属性的文档。最常见的证书类型是符合 `X.509` 标准的证书，允许在其结构中编码一方的识别细节；

- `公钥和私钥`：身份验证和消息完整性是安全通信中的重要概念。身份验证要求确保交换消息的各方创建特定消息的身份。对于具有 `完整性` 的消息，意味着在其传输期间不能被修改；

- `证书颁发机构`：证书颁发机构向不同的参与者分发证书，这些证书由 `CA` 进行数字签名。`CA` 是为组织的参与者提供可验证的数字身份的基础；

- `证书撇销列表`：出于某种原因而被撤销的证书的引用列表。

`PKI` 只是一个体系结构，负责生成及颁发证书。在 `Hyperledger Fabric` 中，默认 `MSP` 实际上使用符合 `X.509` 标准的证书作为身份，采用传统的 `PKI` 分层模型来实现。

`MSP` 是从 `Hyperledger Fabric 1.0` 版本开始抽象出来的一个模块化组件，用于定义身份验证、进行身份验证和允许访问网络的规则。更确切地说，`MSP` 是 `Hyperledger Fabric` 对网络中的组成成员进行身份管理与验证的模块组件，其具体作用如下：

- 管理用户ID；

- 验证想要加入网络的节点：每一个想加入网络的节点必须提供其有效且合法的 `MSP` 信息；

- 为客户发起的交易提供凭证：在各节点（`Client`、`Peer`、 `Orderer`）之间进行数据传输时，需要验证各节点的签名。

`MSP` 在 `Hyperledger Fabric` 中按级别分类如下：

- 网络 `MSP`：对整个 `Hyperledger Fabric` 网络中的成员进行管理；定义参与组织的 `MSP`，以及组织成员中的哪些成员被授权执行管理任务（如创建通道）；

- 通道 `MSP`：对一个通道中的组织成员进行管理。通道在特定的一组组织之间提供私有通信；在该通道的 `MSP` 环境中（通道策略）定义了谁有权限参与通道上的某些行为（如添加组织或实例化链码）；

- `Peer MSP`：本地 `MSP`在每个 `Peer` 节点的文件系统上定义，并且每个 `Peer` 节点都有一个单独的 `MSP` 实例。执行与通道 `MSP` 完全相同的功能，其限制是它仅适用于定义它的 `Peer` 节点；

- `Orderer MSP`：与 `Peer MSP`相同，`Orderer` 节点的本地 `MSP` 也在其节点的文件系统上定义，仅适用于该 `Orderer` 节点；

- `User MSP`：每一个组织都可以拥有多个不同的用户，都在其 `Organization` 节点的文件系统上定义，仅适用于该组织（包括该组织下的所有 `Peer` 节点）。

#####`MSP` 的组成结构
`MSP` 的逻辑结构如下图所示（与实际的物理结构会有所不同）：

<br>

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gc5hpm3ss1j310m0bgt9d.jpg)

<br>

由图可见，`MSP` 有 9 个元素。其中 `MSP` 名称是根文件夹名称，每个子文件夹代表 `MSP` 配置的不同元素。

- `根CA（RCA）`：文件夹包含根 `CA`（`Certificate Authorities`）的自签名 `X.509` 证书列表用于自签名及给中间 `CA` 证书签名；

- `中间CA（ICA）`：包含由根 `CA` 颁发的证书列表；

- `组织单位（OU）`：这些单位包含一个组织单位列表，其成员被视为该`MSP` 所代表的组织的一部分；

- `管理员（B）`：此文件夹包含一个标识列表，用于定义具有此组织管理员角色的角色。对于标准 `MSP` 类型，此列表中应该有一个或多个 `X.509` 证书；

    需要注意，仅仅一个具有管理员的角色，并不意味着他们可以管理特定的资源，给定标识在管理系统方面的实际功能由管理系统资源的策略决定。

- `撤销证书（ReCA）`：保存已被撤销参与者身份的信息；

- `签名证书（SCA）`：背书节点在交易提案响应中的签名证书。此文件夹对于本地 `MSP` 是必需的，并且该节点必须只有一个 `X.509` 证书；

- `私钥（KeyStore）`：此文件夹是为 `Peer` 或 `Orderer` 节点（或客户端的本地 `MSP`）的本地 `MSP` 定义的，并包含节点的签名密钥。此密钥以加密方式匹配 `SCA` 文件夹包含的签名证书，并用于签署数据（如签署交易提议响应，作为认可阶段的一部分）。此文件夹对于本地 `MSP` 是必需的，并且必须只包含一个私钥；

- `TLS 根 CA（TLS RCA）`：包含组织信任的用于 `TLS` 通信的根 `CA` 的自签名 `X.509` 证书列表。此文件夹中必须至少有一个 `TLS` 根 `CA X.509` 证书；

- `TLS 中间 CA（TLS ICA）`：保存由 `TLS` 根 `CA` 颁发的中间证书列表。

#####`MSP` 应用

要想初始化一个 `MSP` 实例，每一个 `Peer` 节点和 `Orderer` 节点都需要在本地指定其配置并启动。

首先，为了方便地在网络中引用 `MSP`，每个 `MSP` 都需要一个特定的名字（如 `OrdererMSP`、`Org1MSP` 或 `Org2Msp.domain.com`）。此名字被称为 `MSP` 标识符或 `MSP ID`。对于每个 `MSP` 实例来说，`MSP` 标识符都必须独一无二。

在系统起始阶段，需要指定在网络中出现的所有 `MSP` 的验证参数，且这些参数需要在系统通道的创世区块中指定。`MSP` 的验证参数包括 `MSP标识符`、`信任源证书`、`中间CA`和`管理员的证书`，以及 `OU说明` 和 `CLR`。系统的创世区块会在 `Orderer` 节点的设置阶段提供
给它们，且允许它们批准创建通道的请求。如果创世区块包含两个具有相同标识符的 `MSP`，那么 `Orderer` 节点将拒绝系统创世区块，导致网络引导程序执行失败。

要想生成 `X.509` 证书以满足 `MSP` 配置，应用程序可以有多种方式实现。

- 使用 `OpenSSL`。在此需要注意，`Hyperledger Fabric` 不支持包括 `RSA密钥` 在内的证书；

- 已使用 `cryptogem` 工具；

- `Hyperledger Fabric CA` 也可用于生成配置 `MSP` 所需的密钥及证书。

在节点的配置文件中，我们需要指定到 `mspconfig` 文件夹的路径，以及节点的 `MSP` 标识符。节点的 `MSP` 标识符会作为参数 `localMspId` 和 `LocaIMSPID` 的值分别提供给 `Peer` 节点和 `Orderer` 节点。

在运行环境中，可以通过为 `Peer` 使用 `CORE` 前缀（如 `CORE_PEER_LOCALMSPID`）及为 `Orderer` 使用 `ORDERER` 前缀（如 `ORDERER_GENERAL_LOCALMSPID`）对以上变量进行重写。例如，`fabric-sample` 提供的 `docker-compose-base.yaml` 示例配置文件中关于 `MSP` 的内容如下：

```yaml
Version: '2'
services:

    orderder.example.com:
        ......
        environment:
            ......
            # 指定本地 MSP ID
            - Orderer_GENERAL_LOCALMSPID=OrdererMSP
            - Orderer_GENERAL_LOCALMSPDIR=/var/hyper ledger/orderer/msp
            ......
        volumes:
            # MSP 映射信息
            - ../crypto-config/ordererOrganizations/example.com/orderers/orderer.example.com/msp:/var/hyperledger/orderer/msp
            # TLS 映射信息
            - ../crypto-config/ordererOrganizations/example.com/orderers/orderer.example.com/tls:/var/hyperledger/orderer/tls
    ......

    peer0.org1.example.con:
        ......
        environment:
            ......
            # 指定本地 MSP ID
            - CORE_PEER_LOCALMSPID=Org1MSP
        volumes:
            # MSP 映射信息
            - ../crypto-config/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/msp:/etc/hyperledger/fabric/msp
            ......

    peer1.org1.example.com:
        ......
        environment:
            ......
            # 指定本地 MSP ID
            - CORE_PEER_LOCALMSPID=Org1MSP
        volumes:
            # MSP 映射信息
            - ../crypto-config/peerOrganizations/org1.example.com/peers/peer1.org1.example.com/msp:/etc/hyperledger/fabric/msp
            ......
```

对本地的 `MSP` 进行重新配置只能通过手动的方式实现，且该过程需要重启 `Peer` 节点和 `Orderer` 节点。

####编程要求

根据相关知识，按照要求完成右侧选择题任务，包含单选题和多选题。

---
开始你的任务吧，祝你成功！