[TOC]

---

####任务描述

本关任务：认真阅读 `Hyperledger Fabric CA` 介绍，并完成 `Hyperledger Fabric CA` 服务启动。

####相关知识

#####CA 简介

`Hyperledger Fabric CA` 是 `Hyperledger Fabric` 的证书颁发机构（`CA`），是 `Hyperledger Fabric` 内一个可选的 `MemberService` 组件，对网络内各个实体的身份证书进行管理，主要功能如下：

- 负责 `Hyperledger Fabric` 网络内所有实体（`Identity`）身份的注册；

- 负责对数字证书的签发，包括 `CErts`（身份证书）、 `TErts`（交易证书）；

- 负责证书的续签或吊销。

访问 `Hyperledger Fabric CA` 服务器可以通过 `Hyperledger Fabric CA` 客户端或其中一个 `Hyperledger Fabric SDK` 来实现，与 `Hyperledger Fabric CA` 服务器的所有通信都是通过 `RESTful API` 进行的。

`Hyperledger Fabric CA` 客户端或 `SDK` 可以连接到 `Hyperledger Fabric CA` 服务器集群，集群由 `HA Proxy` 等实现负载均衡。服务器可能包含多个 `CA` ，每个 `CA` 都是 `根CA` 或 `中间CA` ，每个 `中间CA` 都有一个 `父CA`。

`Hyperledger Fabric CA` 的身份信息保存在数据库或 `LDAP` 中。目前 `Hyperledger Fabric CA` 支持的数据库有 `MySQL`，`PostgreSQL`，`SQLite`，默认使用 `SQLite` 数据库。如果配置了
`LDAP`，则身份信息将保留在 `LDAP` 中，而不是数据库中。

#####CA 安装

**环境要求**：

安装 `Galang 1.9` 或以上版本并设置 `GOPATH` 环境变量。

安装 `libtool` 和 `libltdl-dev` 依赖包。

`CA` 安装有以下两种方式：

1. 通过 `go get` 命令下载 `CA` 服务端与客户端二进制命令到 `$GOPATH/bin` 目录下；

2. 通过 `make` 命令编译 `fabric-ca` 源码，生成 `fabric-ca-server` 和 `fabric-ca-client` 两个二进制可执行文件。

> 注意：由于 `Educoder` 平台已为我们提供了 `CA` 的二进制可执行文件。真实路径位于：`/opt/go/src/github.com/hyperledger/fabric/scripts/fabric-samples/bin/`。所以此处不对其具体安装方式做详细介绍。

#####CA 初始化

`Hyperledger Fabric CA` 服务器在使用之前必须先启动，而在服务器启动之前会进行一系列的初始化工作，包括确定服务器的主目录，生成相关的配置文件、数据库文件、`PEM` 格式的 `CA` 证书文件等。

- 确定 `Hyperledger Fabric CA` 服务器的主目录：

    1. 检查命令行，如果设置了 `-home` 命令行选项，则使用 `home` 的值作为主目录；

    2. 否则，检查 `FABRIC_CA_SERVER_HOME` 环境变量，如果设定了 `FABRIC_CA_SERVER_HOME` 环境变量的值，则使用其值作为主目录；

    3. 否则，检查 `FABRIC_CA_HOME` 环境变量，如果设定了 `FABRIC_CA_HOME` 环境变量的值，则使用其值作为主目录；

    4. 否则，检查 `CA_CFG_PATH` 环境变量，如果设定了 `CA_CFG_PATH` 环境变量的值，使用其值作为主目录；

    5. 否则，使用当前工作目录作为服务器端的主目录。

    现在使用一个当前工作目录作为服务器端的主目录。返回至用户的 `HOME` 目录下，创建一个名为 `fabric-ca` 的目录并进入该目录：

    ```shell
    cd ~
    mkdir fabric-ca && cd fabric-ca
    ```

    创建该目录的目的是将其作为 `Fabric CA` 服务器的主目录。默认服务器主目录为 `./`。

- 初始化 `Hyperledger Fabric CA`：

    初始化 `Hyperledger Fabric CA` 的命令如下所示：

    ```shell
    # 赋予 fabric-ca-server 工具可执行权限
    chmod 775 /opt/go/src/github.com/hyperledger/fabric/scripts/fabric-samples/bin/fabric-ca-server
    # 初始化 CA
    /opt/go/src/github.com/hyperledger/fabric/scripts/fabric-samples/bin/fabric-ca-server init -b admin:pass
    ```

    在初始化时，`-b` 参数是必需的，用于指定注册用户的用户名与密码。

    命令执行后会自动生成如下的相关文件到至当前目录:

    - `fabric-ca-server-config.yaml`：默认配置文件；

    - `ca-cert.pem`：`PEM` 格式的 `CA` 证书文件，自签名；

    - `fabric-ca-server.db`：存放数据的 `SQLite3` 数据库；

    - `msp/keystore/`：路径下存放个人身份的私钥文件（`_sk` 文件）。对应签名证书。

#####CA 启动

快速启动并初始化一个 `fabric-ca-server` 服务：

```shell
fabric-ca-server start -b admin:pass
```

**参数说明**：

- `-b`：提供注册用户的名称与密码，如果没有使用 `LDAP`，则这个选项为必需。默认的配置文件名称为 `fabric-ca-server-config.yaml`。

如果之前没有执行初始化命令，则启动过程中会自动进行初始化操作，即从主配置目录搜索相关证书和配置文件，如果不存在则会自动生成。

####编程要求

按相关内容完成 `链码测试过程简化` 后，点击评测即可。

#####测试说明

平台会检测 `docker ps | awk '{print $2}' | sort`  输出的用于测试链码功能的分布式网络节点结构是否正确。

---
开始你的任务吧，祝你成功！

```go
export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca/clients/admin
chmod 775 /opt/go/src/github.com/hyperledger/fabric/scripts/fabric-samples/bin/fabric-ca-client
/opt/go/src/github.com/hyperledger/fabric/scripts/fabric-samples/bin/fabric-ca-client enroll -u http://admin:pass@localhost:7054

```