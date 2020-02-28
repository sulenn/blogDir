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
    cd /home
    mkdir fabric-ca && cd fabric-ca
    ```

    创建该目录的目的是将其作为 `Fabric CA` 服务器的主目录。默认服务器主目录为 `./`。

- 初始化 `Hyperledger Fabric CA`：

    初始化 `Hyperledger Fabric CA` 的命令如下所示：

    ```shell
    # 切换路径
    cd /data/workspace
    # 继续切换路径
    # （注意该路径中的目录名是随机生成的，每个学生看到的都不一样，需要学生自己动态调整）
    cd myshixun_982070/9aqwf8nrox20200215213056
    # 执行脚本，初始化环境配置（需要花费大约 90 秒）
    mv ./fabric-ca-server /root
    # 切换路径
    cd home/fabric-ca
    # 赋予 fabric-ca-server 工具可执行权限
    chmod 775 ../fabric-ca-server
    # 初始化 CA
    ../fabric-ca-server init -b admin:pass
    ```

    在初始化时，`-b` 参数是必需的，用于指定注册用户的用户名与密码。

    命令执行后会自动生成如下的相关文件到至当前目录:

    - `fabric-ca-server-config.yaml`：默认配置文件；

    - `ca-cert.pem`：`PEM` 格式的 `CA` 证书文件，自签名；

    - `fabric-ca-server.db`：存放数据的 `SQLite3` 数据库；

    - `msp/keystore/`：路径下存放个人身份的私钥文件（`_sk` 文件）。对应签名证书。

    -

    -

#####CA 启动

快速启动并初始化一个 `fabric-ca-server` 服务：

```shell
fabric-ca-server start -b admin:pass
```

**参数说明**：

- `-b`：提供注册用户的名称与密码，如果没有使用 `LDAP`，则这个选项为必需。默认的配置文件名称为 `fabric-ca-server-config.yaml`。

如果之前没有执行初始化命令，则启动过程中会自动进行初始化操作，即从主配置目录搜索相关证书和配置文件，如果不存在则会自动生成。

#####配置数据库

`Hyperledger Fabric CA` 默认数据库为 `SQLite`，默认数据库文件 `fabric-ca-server.db` 位于 `Hyperledger Fabric CA` 服务器的主目录中。`SQLite` 是一个嵌入式的小型数据系统，但在
一些特定的情况下，我们需要集群来支持，所以 `Hyperledger Fabric CA` 也设计了支持其他的数据库系统（目前只支持 `MySQL`、`PostgreSQL` 两种）。

下面我们来看如何配置来实现其对不同数据库的支持。

1. 配置 `PostgreSQL`：

    如果使用 `PostgreSQL` 数据库，则需要在 `Hyperledger Fabric CA` 服务器端的配置文件 `fabric-ca-server-config.yaml` 中进行如下设置：

    ```yaml
    db:
        typo: postgres
        datasource: host=localhost port=5432 user=Username password=Password dbname=fabric_ca sslmode=verify-full
    ```

    如果要使用 `TLS`，则必须指定 `Hyperledger Fabric CA` 服务器配置文件中的 `db.tls` 部分。如果在 `PostgreSQL` 服务器上启用了 `SSL` 客户端身份验证，则还必须在 `db.tls.client` 部分指定客户端证书和密钥文件。如下所示：

    ```yaml
    db：
        ...
        tls:
            enabled: true
            certifiles:
                - db-server-cert.pem
            client:
                certfile: db-client-cert.pem
                keyfile: db-client-key.pem
    ```

    其中 `db.tls.certfiles` 指定 `PEM` 编码的受信任根证书文件列表。`db.tls.client.certfile` 和 `db.tls.client.keyfile` 指定 `Hyperledger Fabric CA` 服务器与 `PostgreSQL` 服务器进行安全通信的 `PEM` 编码的证书及密钥文件，作用于服务器与数据库之间的 `TLS` 连接。
    生成自签名证书的相关内容可参考官方说明：`https://www.postgresql.org/docs/9.5/static/ssl-tcp.html`，需要注意的是，自签名证书仅用于测试目的，不应在生产环境中使用。

2. 配置 `MySQL`

    如果使用 `MySQL` 数据库，则需要在 `Hyperledger Fabric CA` 服务器端的配置文件进行如下设置：

    ```yaml
    db:
    type: mysql
    datasource: root:rootpw@tcp(localhost:3306)/fabric-ca?parseTime=true&tls=cuatom
    ```

    如果通过 `TLS` 连接到 `MySQL` 服务器，则还需要配置 `db.tls.client` 部分，如 `PostgreSQL` 部分所述。

#####配置 `LDAP`

`Hyperledger Fabric CA` 服务器可以通过服务器端的配置连接到指定 `LDAP`（`Lightweight Directory Access Protocol`，轻量目录访问协议）服务器。之后可以执行以下操作。

- 在注册之前读取信息进行验证；

- 对用于授权的标识属性值进行验证；

- 修改 `Hyperledger Fabric CA` 服务器的配置文件中的 `LDAP` 部分：

    ```yaml
    ldap:
    enabled: false
    url: <scheme>://adminDN>:<adminPassword>@<host>:<port>/<base>
    userfilter: <filter>
    attribute:
        names: <LDAPAttrs>
        converters:
            - name: <fcaAttrName>
              value: <fcaExpr>
        mpas:
            <mapName>
                - name: <form>
                  value: <to>
    ```

配置信息中各部分解释如下：

- `scheme`：为 `ldap` 或 `ldaps`；

- `adminDN`：`admin` 用户的唯一名称；

- `adminPassword`：`admin` 用户的密码；

- `host`：`LDAP` 服务器的主机名或 `IP` 地址；

- `port`：可选的端口号，默认 `LDAP` 为 389，`LDAPS` 为 636；

- `base`：用于搜索的 `LDAP` 树的可选根路径；

- `filter`：将登录用户名转换为可分辨名称时使用的过滤器；

- `LDAPAttrs`：一个 `LDAP` 属性名称数组，代表用户从 `LDAP` 服务器请求的特定属性；

- `attribute.converters`：部分用于将 `LDAP` 属性转换为结构 `CA` 属性，其中 `fcaAttrName` 是结构 `CA` 属性的名称；`fcaExpr` 是一个表达式。例如，假设 `<LDAPAttrs>` 是 `["uid"]`，`<fcaAttrName>` 是 `hf.Revoke`，而 `<fcaExpr>` 是 `attr("uid")=~"revoker *"`。这意味用
户从 `LDAP` 服务器请求名为 `uid` 的属性。如果用户的 `uid LDAP` 属性的值以 `revoker` 开头，则为 `hf.Revoker` 属性赋予用户 `true` 的值；否则，为 `hf.Revoker` 属性赋予用户 `false` 的值；

- `attribute.maps`：部分用于映射 `LDAP` 响应值。典型的用例是将与 `LDAP` 组关联的可分辨名称映射到标识类型。

配置好 `LDAP` 后，用户注册的过程如下。

1. `Hyperledger Fabric CA` 客户端或客户端 `SDK` 发送带有基本授权头的注册请求。

2. `Hyperledger Fabric CA` 服务器接收注册请求，解码授权头中的身份名称和密码，使用配置文件中的 `userfilter` 查找与身份名称关联的 `DN` （专有名称），然后尝试让 `LDAP` 绑定用户身份的密码。如果 `LDAP` 绑定成功，则注册通过。

####编程要求

按相关内容完成 `CA 介绍与服务启动` 后，点击评测即可。

#####测试说明

平台会检测 `ls /home/fabric-ca` 命令输出的由初始化 `Hyperledger Fabric CA` 生成的文件目录是否正确。

---
开始你的任务吧，祝你成功！
