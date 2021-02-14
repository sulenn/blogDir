[TOC]

---

####任务描述

本关任务：认真阅读相关知识，并完成 `Hyperledger Fabric CA` 客户端命令应用。

####相关知识

`Hyperledger Fabric CA` 可以采用客户端命令行或 `RESTful API` 在内的两种方式与 `Hyperledger Fabric CA` 服务端进行交互，其中最方便的方式是使用客户端工具 `fabric-ca-client`。

`Hyperledger Fabric CA` 客户端的主目录路径设置如下：

- 如果设置了 `-home` 命令行选项，则以此值为首选；

- 如果没有设置 `-home`，则查找 `FABRIC_CA_CLIENT_HOME` 值；

- 否则，查找 `FABRIC_CA_HOME` 值；

- 否则，查找 `CA_CFG_PATH` 值；

- 如果都未设置，则使用 `$HOME/.fabric-ca-client` 作为客户端的主目录。

#####`Hyperledger Fabric CA` 客户端命令

`fabric-ca-client` 命令可以与服务端进行交互，包括 5 个子命令。

- `enroll`：注册获取 `ECert`；

- `register`：登记用户；

- `getcainfo`：获取 `CA` 服务的证书链；

- `reenroll`：重新注册；

- `revoke`：撤销签发的证书身份；

- `version`：`Hyperledger Fabric CA` 客户端版本信息。

这些命令在执行时都是通过服务端的 `RESTful` 接口来进行操作的。

#####注册用户

打开一个新的终端。首先，设置 `Hyperledger Fabric CA` 客户端主目录。通过调用在 7054 端口运行的 `Hyperledger Fabric CA` 服务器来注册 `ID` 为 `admin` 且密码为 `pass` 的标识。执行命令如下:

```shell
# 设置客户端主目录
export FABRIC_CA_CLIENT_HOME=/home/clients/admin
# 提供工具可执行权限
chmod 775 /opt/go/src/github.com/hyperledger/fabric/scripts/fabric-samples/bin/fabric-ca-client
# 注册标识（注意执行该命令必须保证上一关实训中的 `Hyperledger Fabric CA` 处于启动状态）
/opt/go/src/github.com/hyperledger/fabric/scripts/fabric-samples/bin/fabric-ca-client enroll -u http://admin:pass@localhost:7054
```

**参数说明**：

- `-u`：进行连接的 `fabric-ca-server` 服务地址。

`enroll` 命令访问指定的 `Hyperledger Fabric CA` 服务，采用 `admin` 用户进行注册。在 `Hyperledger Fabric CA` 客户端主目录下创建配置文件 `fabric-ca-client-config.yaml` 和 `msp` 子目录，存储注册证书（`ECert`），以及相应的私钥和 `CA` 证书链 `PEM` 文件。我们可以在终端输中看到指示 `PEM` 文件存储位置的相关信息。

注册标识成功后，控制台输出如下日志信息：

<br>

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gcdl3276f1j30zp0bbwh6.jpg)

<br>

生成的文件结构如下所示：

<br>

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gcdl6tg5x7j30xh036gly.jpg)

<br>

#####登记用户

注册成功后的用户可以使用 `register` 命令发起登记请求，而设置登记请求的目的是授予注册标识类型适当的权限。`Hyperledger Fabric CA` 服务器会在注册用户登记期间进行 3 次授权检查。

1）注册者（即调用者）必须具有 `hf.Registrar.Roles` 属性，其中包含逗号分隔的值列表，其中一个值等于要注册的身份类型。例如，如果注册者具有值为 `peer`、`app`、`user` 的 `hf.Registrar.Roles` 属性，则注册者可以注册 `Peer`、`App` 和 `User` 类型的身份，但不能注册 `Orderer` 类型的身份；

2）注册者登记其范围内的用户。例如，具有 `a.b` 从属关系的注册者可以登记具有 `a.b.c` 从属关系的身份，但是不可以登记具有 `a.c` 的非直接从属关系的身份。如果登记请求中未指定任何从属关系，则登记的身份将被授予注册者同样的归属范围；

3）如果满足以下所有条件，注册者可以指定登记用户属性。

- 注册者可以登记具有前缀 `hf` 的 `Hyperledger Fabric CA` 保留属性（只有当注册者拥有该前级为 `hf` 的属性并且它是 `hf.Registrar.Attributes` 属性的值的一部分时）。此外，如果属性的类型为列表，则登记的属性值必须等于注册者具有的值的一个子集。如果属性的类型为 `boolean`，则只有当注册者的属性值为 `true` 时，注册者才能登记该属性。

- 注册自定义属性（即名称不以 `hf.` 开头的任何属性）要求注册者具有 `hf.Registrar.Attributes` 属性，其中包含要注册的属性或模式的值。唯一支持的模式是末尾带有 `“·”` 的字符串。例如， `“ab.*”` 是匹配以 `“ab”` 开头的所有属性名称的模式。例如，如果注册者具有 `hf.Registrar.Attributes = orgAdmin`，则注册者可以在身份中添加或删除唯一的 `orgAdmin` 属性。

- 如果请求的属性名称为 `hf.Registrar.Attributes`，则执行附加检查以查看此属性的请求值是否等于 `hf.Registrar.Attributes` 的注册者值的子集。例如，如果注册者的 `hf.Registrar.Attributes` 的值是 `“ab.*, x.y.z”`，并且所请求的属性值是 `“a.b.c, x.y.z”`，那么它是有效的，因为 `a.b.c` 匹配 `a.b*, x.y.z` 匹配注册者的 `x.y.z` 值。

根据如上说明，我们列举一个示例：使用一个管理员标识的凭据注册登记 `ID` 为 `admin2` 的新用户，从属关系为 `org1.department1`，名为 `hf.Revoker` 的属性值为 `true`，以及名为 `admin` 的属性值为 `true`。`:ecert` 后缀表示默认情况下，`admin` 属性及其值将插入用户的注册证书中，实现访问控制决策，命令如下：

 ```shell
/opt/go/src/github.com/hyperledger/fabric/scripts/fabric-samples/bin/fabric-ca-client register --id.name admin2 --id.affiliation org1.department1 --id.attrs 'hf.Revoker=true,admin=true:ecert'
```

**执行后输出**:

<br>

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gcdlf4huulj30zw04sgmh.jpg)

<br>

命令执行成功后返回该新登记用户的密码。如果想使用指定的密码，则在命令中添加选项 `--id.secret password` 即可。

登记时可以将多个属性指定为 `-id.attrs` 标志的一部分，每个属性必须以逗号分隔。对于包含逗号的属性值，必须将该属性封装在双引号中。

#####登记注册节点

登记 `Peer` 或 `Orderer` 节点的操作与登记用户身份类似；可以通过 `-M` 指定本地 `MSP` 的根路径以在其下存放证书文件。

下面登记一个名为 `peer` 的节点，登记时指定密码，而不是让服务器为其生成。

1）登记节点：

```shell
/opt/go/src/github.com/hyperledger/fabric/scripts/fabric-samples/bin/fabric-ca-client register --id.name peer1 --id.type peer --id.affiliation org1.department1 --id.secret peer1pw
```

**执行后输出**:

<br>

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gcdlkf6lvsj30zx04i3za.jpg)

<br>

2）注册节点:

```shell
export FABRIC_CA_CLIENT_HOME=/home/clients/peer1
/opt/go/src/github.com/hyperledger/fabric/scripts/fabric-samples/bin/fabric-ca-client enroll -u http://peer1:peer1pw@localhost:7054 -M $FABRIC_CA_CLIENT_HOME/msp
```

**执行后输出**:

<br>

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gcdm06h5dzj30zq0bfaci.jpg)

<br>

**参数说明**：

- `-M`：指定生成证书存放目录 `MSP` 的路径，默认为 `msp`。

命令执行成功后会在 `FABRIC_CA_CLIENT_HOME` 目录下生成指定的 `MSP` 目录，在此目录下生成 `MSP` 的私钥和证书。

####编程要求

按相关内容完成 `CA 客户端命令应用` 后，点击评测即可。

#####测试说明

平台会检测 `ls /home/fabric-ca/clients/admin` 命令输出的文件目录是否正确。

---
开始你的任务吧，祝你成功！
