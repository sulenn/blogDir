[TOC]

---

####任务描述

本关任务：根据本实训的相关知识，完成与 Hyperledger Fabric 核心配置文件介绍相关的选择题。

####相关知识

Hyperledger Fabric 中有两个示例配置文件，一个为 Peer 节点的示例配置文件，另一个为 Orderer 节点的实例配置文件。理解这两个配置文件的内容有助于更近一步地理解 Hyperledger Fabric 运行状况。

#####core.yaml 配置文件介绍

core.yaml 配置文件是 Peer 节点的示例配置文件，由如下六大部分内容组成。

1. 日志部分

    日志记录级别有 6 种: CRITICAL、 ERROR、 WARNING、 NOTICE、INFO、 DEBUG。

    在配置文件中，使用 level 属性指定全局的目志记录级别；然后单独指定 cauthdsl、gossip、grpc、ledger、msp、policies、peer 的 gossip 模块的日志级别，用来覆盖默认的全局日志级别。
    ssip, gpe,lger.msp、 policies, peer的gosp模块的日志级别。

2. peer 部分

    peer 部分是 Peer 服务的核心配置内容，包括 peer 基础服务部分、gossip 部分、events、tls 部分、BCCSP 部分等相关配置信息。

    - peer 基础服务部分主要指定 Peer 节点的监听地址、端口号信息，以及客户端与 Peer 节点的连接信息等。

    - gossip 部分主要指定节点角色（Leader 节点）的方式、区块/消息的大小及间隔时间等信息。

    - events 部分主要指定事件监听地址、端口号、缓冲数、超时等信息。

    - tls 部分主要指定证书及密钥的相关信息。

    - BCCSP 分布主要指定区块链的加密实现方式，默认为 SW（SoftWare），即软件基础的加密方式。

3. vm 部分

    vm 部分主要配置链码运行的环境，目前主要支持 Docker 容器。

4. 链码部分

    链码部分是与链码相关的配置，主要指定了链码的路径、链码的名称、构建环境、链码容器启动超时、系统链码启用信息、链码容器的日志设置信息等。

5. ledger 部分

    ledger 部分是分类账本的配置信息，主要指定如下内容。

    - blockchain：区块链配置信息，默认无指定。

    - state：指定状态数据库，默认使用 goleveldb 作为状态记录数据库，如果不适用 goleveldb，则可以配置 CouchDB 数据库的相关信息。

    - history：是否开启历史记录功能。

6. metrics 部分

    metrics 是一个系统性能度量框架，主要用来实现对服务进行监控、统计；指定是否启动 metrics 服务器，当启用 metrics 服务器后，关联指定相应的类型、报告度量的频率及服务
    器相关的信息。

#####orderer.yaml 配置文件介绍

orderer.yaml 配置文件是 Orderer 节点的示例配置文件，共指定了如下五大部分内容。

1. General 部分

    General 部分是 orderer.yam 配置文件的基础配置信息部分，主要指定配置如下：

    - LedgerType：指定分类账本类型。

    - ListenAddress 与 ListenPort：指定节点监听地址及端口号。

    - TLS 部分：指定是否启用 TLS 验证、TLS证书、签名私钥、信任的根 CA 证书信息。

    - Keepalive：指定与客户端的连接信息。

    - LogLevel 与 LogFormat 指定日志级别与日志输出格式。

    - GenesisMethod、GenesisProfile、GenesisFile：指定生成初始区块相关的信息。

    - LocalMSPDir 与 LocalMSPID：指定 MSP 目录所在路径及 MSP 的 ID。

    - BCCSP 部分：指定区块链的加密实现方式，默认为 SW（ SoftWare），即软件基础的加密方式。

2. FileLedger 部分

    如果指定分类账本类型为文件类型，则通过 FileLedger 部分配置文件账本的相关信息。

    - Location：指定区块链的本地存储路径。

    - Prefix：指定临时空间中的前缀名称（未指定 Location 时）。

3. RAMledger 部分

    如果分类账本指定不使用文件账本类型，而是内存账本类型时，则需要通过 HistorySize 属性指定在内存中保存区块的最大数量。

4. Kafka 部分

    如果 Orderer 服务使用 Kaka 实现排序服务，则进行相关的配置信息指定：

    - Rery：指定连接到 Kafka 的重试请求信息。

    - Verbose：指定是否启用日志记录。

    - TLS：指定 Orderer 连接到 Kafka 的 TLS 相关设置，包括是否启动 TLS，指定 TLS 密钥、证书及可信任的 CA 根证书。

    - Version：指定 Kafka 的版本信息。

5. Debug 部分

    此部分配置信息相对简单，主要指定广播服务与交付服务的请求保存目录。

####编程要求

根据相关知识，按照要求完成右侧选择题任务，其中第一题为多选题，第二、三题为单选题。

---
开始你的任务吧，祝你成功！