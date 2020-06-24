## IPV6

FISCO BCOS 新增对 IPv6 的支持，RPC、CHANNEL 和 P2P 均可使用 IPv6 进行网络连接、通信。

传统的 IPv4 网络地址位数只有32位，可表示的 IP 地址数量有限，无法满足当前各种物联网电子设备的需求。而 IPv6 将网络地址位数扩展到 128 位，代表着可以为地球上任何需要联网的设备提供唯一确定的 IP 地址，极大提高了网络效率。

## 测试内容

通过本地和远程 IPv6 测试机两种方式测试。其中远程使用两台 IPV6 测试机进行测试。

**测试点**：

1. p2p 测试
2. channel 测试
3. rpc 测试

**fisco bcos 版本**：手动编译 [dev 分支](https://github.com/FISCO-BCOS/FISCO-BCOS.git)

**console工具**：[https://github.com/FISCO-BCOS/console/releases/tag/v1.0.10](https://github.com/FISCO-BCOS/console/releases/tag/v1.0.10)

**web3sdk**：手动编译 [dev分支](https://github.com/FISCO-BCOS/web3sdk.git)

以下是本地测试的步骤

### p2p 测试

1. 搭建单群组四节点区块链网络，可参考：[安装](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/installation.html)。注意使用 `-e` 指定手动编译的 fisco-bcos 二进制包

2. 修改四个节点的 config.ini 配置文件设置

```shell
[p2p]
    listen_ip=::1
    listen_port=30300    # 注意 node1、node2、node3 为 30301、30302、30303
    ;enable_compress=true
    ; nodes to connect
    node.0=[::1]:30300
    node.1=[::1]:30301
    node.2=[::1]:30302
    node.3=[::1]:30303
```

3. 启动所有节点，查看节点之间是否正常共识

```shell
# 启动4个节点
bash nodes/127.0.0.1/start_all.sh
# 查看节点共识
tail -f nodes/127.0.0.1/node0/log/log* | grep +++
```

### channel 测试

1. 修改四个节点的 config.ini 配置文件设置

```ini
[rpc]
    channel_listen_ip=::1
    jsonrpc_listen_ip=::1
    channel_listen_port=20200
    jsonrpc_listen_port=8545
```

2. 重启所有节点

```shell
bash nodes/127.0.0.1/stop_all.sh && bash nodes/127.0.0.1/start_all.sh
```

3. 下载、解压 console 工具。修改 applicationContext-sample.xml 文件为 applicationContext.xml。将 ca 证书和 sdk 私钥、证书拷贝至 ./console/conf/ 文件夹目录。

```shell
# 下载、解压 console 工具，注意下载版本为 1.0.10
curl -LO https://github.com/FISCO-BCOS/console/releases/download/v1.0.10/download_console.sh && bash download_console.sh

# 拷贝控制台配置文件
cp -n console/conf/applicationContext-sample.xml console/conf/applicationContext.xml

# 配置控制台证书
cp nodes/127.0.0.1/sdk/* console/conf/
```

4. 修改控制台配置文件

```xml
......
<property name="allChannelConnections">
            <list>
                <bean id="group1"  class="org.fisco.bcos.channel.handler.ChannelConnections">
                    <property name="groupId" value="1" />
                    <property name="connectionsStr">
                        <list>
                            <value>[::1]:20200</value>
                        </list>
                    </property>
                </bean>
            </list>
</property>
......
```

5. 替换手动编译的 web3sdk.jar，编译方法可参考：[web3sdk](https://github.com/FISCO-BCOS/web3sdk)

```shell
# 将编译好的 web3sdk.jar 替换 console 中的 web3sdk-2.5.0.jar
cp dist/apps/web3sdk.jar /home/qiubing/FISCO-BCOS-CHAIN/fisco-nosm-dev.ipv6/console/lib/web3sdk-2.5.0.jar  # 注意替换路径
```

6. 启动 console，部署合约，查看出块情况

```shell
# 启动 console
bash console/start.sh

# 部署合约
[group:1]> deployByCNS HelloWorld 1.0
contract address: 0x6a7c4fb263cf42028f3a48d1a46e191972c887ae

# 块高
[group:1]> getBlockNumber
2
```

### rpc 测试

使用 curl 命令测试 rpc 功能

```shell
# 查看节点客户端版本信息
> curl -X POST --data '{"jsonrpc":"2.0","method":"getClientVersion","params":[],"id":1}' http://\[::1\]:8545 |jq
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "Build Time": "20200624 15:44:43",
    "Build Type": "Linux/g++/RelWithDebInfo",
    "Chain Id": "1",
    "FISCO-BCOS Version": "2.6.0",
    "Git Branch": "dev",
    "Git Commit Hash": "da03cf168b03fae7d4dbb37248a6c250b88ce7f2",
    "Supported Version": "2.4.0"
  }
}
```

