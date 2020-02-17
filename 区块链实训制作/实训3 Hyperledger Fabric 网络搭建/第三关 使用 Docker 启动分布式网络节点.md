[TOC]

---

####任务描述

本关任务：使用 Dokcer 为 Hyperledger Fabric 网络启动分布式节点。

####相关知识

通过第一、二关的训练，我们已经将网络启动之前所需的所有内容准备就绪，下面深入分析网络中各节点运行时所需指定的必备信息。

#####配置网络服务

启动网络，就是启动提供网络服务的各个节点。由于要启动多个网络节点，`Hyperledger Fabric` 采用了容器技术，所以需要一个简化的方式来集中化管理这些节点容器。我们使用 `docker-compose` 这个工具来实现一步到位的节点容器管理，而且只需要编写相应的配置文件即可。

`Hyperledger Fabric` 同样提供了 `docker-compose` 工具的示例配置文件，该配置文件在 `/opt/go/src/github.com/hyperledger/fabric/scripts/fabric-samples/first-network` 目录下，文件名称为 `docker-compose-cli.yaml`，打开这个配置文件可以看到如下完整内容：

```yaml
version: '2'

volumes:
  orderer.example.com:
  peer0.org1.example.com:
  peer1.org1.example.com:
  peer0.org2.example.com:
  peer1.org2.example.com:

networks:
  byfn:

services:

  orderer.example.com:
    extends:
      file:   base/docker-compose-base.yaml
      service: orderer.example.com
    container_name: orderer.example.com
    networks:
      - byfn

  peer0.org1.example.com:
    container_name: peer0.org1.example.com
    extends:
      file:  base/docker-compose-base.yaml
      service: peer0.org1.example.com
    networks:
      - byfn

  peer1.org1.example.com:
    container_name: peer1.org1.example.com
    extends:
      file:  base/docker-compose-base.yaml
      service: peer1.org1.example.com
    networks:
      - byfn

  peer0.org2.example.com:
    container_name: peer0.org2.example.com
    extends:
      file:  base/docker-compose-base.yaml
      service: peer0.org2.example.com
    networks:
      - byfn

  peer1.org2.example.com:
    container_name: peer1.org2.example.com
    extends:
      file:  base/docker-compose-base.yaml
      service: peer1.org2.example.com
    networks:
      - byfn

  cli:
    container_name: cli
    image: hyperledger/fabric-tools:$IMAGE_TAG
    tty: true
    stdin_open: true
    environment:
      - GOPATH=/opt/gopath
      - CORE_VM_ENDPOINT=unix:///host/var/run/docker.sock
      - FABRIC_LOGGING_SPEC=INFO
      - CORE_PEER_ID=cli
      - CORE_PEER_ADDRESS=peer0.org1.example.com:7051
      - CORE_PEER_LOCALMSPID=Org1MSP
      - CORE_PEER_TLS_ENABLED=true
      - CORE_PEER_TLS_CERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/server.crt
      - CORE_PEER_TLS_KEY_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/server.key
      - CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
      - CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric/peer
    command: /bin/bash
    volumes:
        - /var/run/:/host/var/run/
        - ./../chaincode/:/opt/gopath/src/github.com/chaincode
        - ./crypto-config:/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/
        - ./scripts:/opt/gopath/src/github.com/hyperledger/fabric/peer/scripts/
        - ./channel-artifacts:/opt/gopath/src/github.com/hyperledger/fabric/peer/channel-artifacts
    depends_on:
      - orderer.example.com
      - peer0.org1.example.com
      - peer1.org1.example.com
      - peer0.org2.example.com
      - peer1.org2.example.com
    networks:
      - byfn
```

由以上配置信息可以看出，该配置文件指定了网络中各个节点容器（共计 6 个容器，即 1 个 `Orderer`、属于 2 个 `Orgs` 组织的 4 个 `Peer`、1 个 `CL1`）的信息。仔细观察会发现，`Orderer` 与各 `Peer` 容器都设置了 `container_name` 与 `networks` 信息；其他信息都由 `extends` 指向了 `base/docker-compose-base.yaml` 文件。

`CLI` 容器指定了所代表的 `Peer` 节点（CORE_PEER_ADDRESS = peer0.orgl.example.com:7051），通过 `volumes` 指定了将系统中的链码、组织结构及证书、生成的配置文件映射到容器中指定的目录下，且通过 `depends_on` 属性指定了所依赖的相关容器。

##### Orderer 和各 Peer 节点的主要配置信息

在 `docker-compose-cli.yaml` 配置文件中，由 `extends.file` 指向了一个 `base/docker-compose.yaml` 的配置文件，命令行工具中的存放路径为：`/opt/go/src/github.com/hyperledger/fabric/scripts/fabric-samples/first-network/base/docker-compose.yaml`。该配置文件指定了 `Orderer` 节点与 `Peer` 节点的主要配置信息。

`Orderer` 节点容器设置如下信息：

1. `environment`：该部分主要关注如下核心配置信息。

	- `ORDERER_GENERAL_GENESISFILE`：指定在 `Orderer` 容器中初始区块的所在路径，由 `volumes` 中的  `../channel-artifacts/genesis.block:/var/hyperledger/orderer/orderer.genesis.block` 指定主机到 `Docker` 的映射。
	
	- `ORDERER_GENERAL_LOCALMSPID`：指定当前 `Orderer` 容器的唯一 `MSPID`。

	- `ORDERER_GENERAL_LOCALMSPDIR`：指定当前 `Orderer` 容器的 `MSP` 所在路径。

	- `ORDERER_GENERAL_TLS_ENABLED`：是否开启 `TLS` 验证。

	- `ORDERER_GENERAL_TLS_PRIVATEKEY`：指定私钥所在路径。

	- `ORDERER_GENERAL_TLS_CERTIFICAT`：指定证书所在路径。

	- `ORDERER_GENERAL_TLS_ROOTCAS`：指定受信任的 `CA` 根证书所在路径。

2. `working_dir`：进入容器后的默认工作目录。

3. `volumes`：指定系统中的初始区块配置文件、`MSP`、`TLS` 目录映射到 `Docker` 容器中的指定路径下。

4. `ports`：指定当前节点的监听端口。

各 `Peer` 节点容器设置信息如下：

1. `extends`：基本信息来源于哪个文件。

2. `environment`：指定容器的 `ID`、监听地址及端口号、本地 `MSPID`，大体与 `Orderer` 中的 `environment` 部分相同。

3. `volumes`：将系统的 `msp` 及 `tls` 目录映射到容器中的指定路径下。

4. `ports`：指定当前节点的监听端口。

配置文件部分信息如下:

```yaml
version: '2'

services:

  orderer.example.com:
    container_name: orderer.example.com
    image: hyperledger/fabric-orderer:$IMAGE_TAG
    environment:
      - FABRIC_LOGGING_SPEC=INFO
      - ORDERER_GENERAL_LISTENADDRESS=0.0.0.0
      - ORDERER_GENERAL_GENESISMETHOD=file
      - ORDERER_GENERAL_GENESISFILE=/var/hyperledger/orderer/orderer.genesis.block
      - ORDERER_GENERAL_LOCALMSPID=OrdererMSP
      - ORDERER_GENERAL_LOCALMSPDIR=/var/hyperledger/orderer/msp
      - ORDERER_GENERAL_TLS_ENABLED=true
      - ORDERER_GENERAL_TLS_PRIVATEKEY=/var/hyperledger/orderer/tls/server.key
      - ORDERER_GENERAL_TLS_CERTIFICATE=/var/hyperledger/orderer/tls/server.crt
      - ORDERER_GENERAL_TLS_ROOTCAS=[/var/hyperledger/orderer/tls/ca.crt]
      - ORDERER_KAFKA_TOPIC_REPLICATIONFACTOR=1
      - ORDERER_KAFKA_VERBOSE=true
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric
    command: orderer
    volumes:
    - ../channel-artifacts/genesis.block:/var/hyperledger/orderer/orderer.genesis.block
    - ../crypto-config/ordererOrganizations/example.com/orderers/orderer.example.com/msp:/var/hyperledger/orderer/msp
    - ../crypto-config/ordererOrganizations/example.com/orderers/orderer.example.com/tls/:/var/hyperledger/orderer/tls
    - orderer.example.com:/var/hyperledger/production/orderer
    ports:
      - 7050:7050

  peer0.org1.example.com:
    container_name: peer0.org1.example.com
    extends:
      file: peer-base.yaml
      service: peer-base
    environment:
      - CORE_PEER_ID=peer0.org1.example.com
      - CORE_PEER_ADDRESS=peer0.org1.example.com:7051
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer1.org1.example.com:7051
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer0.org1.example.com:7051
      - CORE_PEER_LOCALMSPID=Org1MSP
    volumes:
        - /var/run/:/host/var/run/
        - ../crypto-config/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/msp:/etc/hyperledger/fabric/msp
        - ../crypto-config/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls:/etc/hyperledger/fabric/tls
        - peer0.org1.example.com:/var/hyperledger/production
    ports:
      - 7051:7051
      - 7053:7053

  ......

  peer1.org2.example.com:
    container_name: peer1.org2.example.com
    extends:
      file: peer-base.yaml
      service: peer-base
    environment:
      - CORE_PEER_ID=peer1.org2.example.com
      - CORE_PEER_ADDRESS=peer1.org2.example.com:7051
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer1.org2.example.com:7051
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer0.org2.example.com:7051
      - CORE_PEER_LOCALMSPID=Org2MSP
    volumes:
        - /var/run/:/host/var/run/
        - ../crypto-config/peerOrganizations/org2.example.com/peers/peer1.org2.example.com/msp:/etc/hyperledger/fabric/msp
        - ../crypto-config/peerOrganizations/org2.example.com/peers/peer1.org2.example.com/tls:/etc/hyperledger/fabric/tls
        - peer1.org2.example.com:/var/hyperledger/production
    ports:
      - 10051:7051
      - 10053:7053
```

#####各 Peer 节点的公共配置信息

在 `base/docker-compose-base.yaml` 配置文件中，由 `extends.file` 指向了一个 `peer-base.yaml` 的配置文件，命令行工具中的存放路径为：`/opt/go/src/github.com/hyperledger/fabric/scripts/fabric-samples/first-network/base/peer-base.yaml`。该配置文件设置了所有 `Peer` 容器的基本共同信息，其核心配置信息如下：

- `CORE_PEER_TLS_ENABLED`：指定是否开启 `TLS` 验证。

- `CORE_PEER_GOSSIP_USELEADERELECTION`：指定使用选举方式。

- `CORE_PEER_GOSSIP_ORGLEADER`：指定是否将当前节点设定为  `Leader`。

- `CORE_PEER_TLS_CERT_FILE`：指定 `TLS` 证书所在路径。

- `CORE_PEER_TLS_KEY_FILE`：指定密钥所在路径。

- `CORE_PEER_TLS_ROOTCERT_FILE`：指定受信任的 `CA` 根证书所在路径。

配置文件完整内容如下:

```yaml
version: '2'

services:
  peer-base:
    image: hyperledger/fabric-peer:$IMAGE_TAG
    environment:
      - CORE_VM_ENDPOINT=unix:///host/var/run/docker.sock
      - CORE_VM_DOCKER_HOSTCONFIG_NETWORKMODE=${COMPOSE_PROJECT_NAME}_byfn
      - FABRIC_LOGGING_SPEC=INFO
      - CORE_PEER_TLS_ENABLED=true
      - CORE_PEER_GOSSIP_USELEADERELECTION=true
      - CORE_PEER_GOSSIP_ORGLEADER=false
      - CORE_PEER_PROFILE_ENABLED=true
      - CORE_PEER_TLS_CERT_FILE=/etc/hyperledger/fabric/tls/server.crt
      - CORE_PEER_TLS_KEY_FILE=/etc/hyperledger/fabric/tls/server.key
      - CORE_PEER_TLS_ROOTCERT_FILE=/etc/hyperledger/fabric/tls/ca.crt
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric/peer
    command: peer node start
```

#####启动网络节点

所有有关 `Hyperledger Fabric` 网络环境所需的文件被创建和配置完成之后（组织结构及身份证书、初始区块文件、通道交易配置文件及锚节点更新配置文件），就可以启动网络。

下面使用已安装的 `docker-compose` 工具，通过命令来方便地启动 `Hyperledger Fabric` 网络的所有节点：

```shell
# 切换路径
cd /opt/hyperledger-fabric-images
# 加载镜像
docker load -i fabric-baseos.tar
docker load -i fabric-ccenv.tar
docker load -i fabric-orderer.tar
docker load -i fabric-peer.tar
docker load -i fabric-tools.tar
# 更新镜像 tag
docker tag hyperledger/fabric-ccenv:1.4.0 hyperledger/fabric-ccenv:latest
docker tag hyperledger/fabric-orderer:1.4.0 hyperledger/fabric-orderer:latest
docker tag hyperledger/fabric-peer:1.4.0 hyperledger/fabric-peer:latest
docker tag hyperledger/fabric-tools:1.4.0 hyperledger/fabric-tools:latest
# 切换路径
cd /opt/go/src/github.com/hyperledger/fabric/scripts/fabric-samples/first-network/
# 设置工具权限
chmod -R 755 /opt/go/src/github.com/hyperledger/fabric/scripts/fabric-samples/bin/
# 启动 Hyperledger Fabric 网络的所有节点
# （注意这里需要用到第一、二关生成的所有文件；如果出错则重新运行第一、二关中使用过的命令）
docker-compose -f docker-compose-cli.yaml up -d
```

**参数说明**：
- `-f`：指定启动容器时所使用的 `docker-compose` 配置文件。

- `-d`：指定是否显示网络启动过程中的实时日志信息，如果需要查看详细网络启动日志，则可以不提供此参数

命令执行后终端输出如下信息：

<br>

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbw7y3g1h5j30p509wabf.jpg)

<br>

`Peer` 节点启动后，默认情况下没有加入网络中的任何应用通道，也不会与 `Orderer` 服务建立连接。需要通过客户端对其进行操作，让它加入网络和指定的应用通道中。

网络启动之后使用如下命令查看网络信息：

```shell
docker ps
```

<br>

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbw848be8aj31570bbwgl.jpg)

<br>

命令执行后会发现有 6 个容器处于活动状态（分别为 `cli`、`peer1.org1.example.com`、`peerl.org2.example.com`、`peer0.org1.example.com`、`orderer.example.com`、`peer0.org2.example.com`），说明网络启动成功。

`docker-compose` 命令有使用许多子命令，从而实现不同的操作。其中，`up` 子命令用于根据指定的配置文件启动相应的容器（网络环境）；`down` 子命令用于关闭已启动的容器（网络环境）。

例如，使用指定的 `docker-compose-cli.yaml` 配置文件关闭网络：

```shell
docker-compose -f docker-compose-cli.yaml down
```

####编程要求

按要求完成本关卡内容后，点击评测即可。平台会检测 `docker ps | awk '{print $2}' | sort` 输出的分布式网络节点结构是否正确。

---
开始你的任务吧，祝你成功！
