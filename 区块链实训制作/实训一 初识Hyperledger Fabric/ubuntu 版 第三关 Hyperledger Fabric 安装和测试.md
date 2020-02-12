[TOC]

---

####任务描述

本关任务：完成 Hyperledger Fabric 安装，并测试运行区块链网络

####相关知识

Hyperledger Fabric 网络环境的构成较为复杂，由 N 个节点组成一个分布式网络，每个节点都有自己的实体身份表示；而且 Hyperledger Fabric 可以通过通道将一个网络分割成不同的私有子网，从而实现不同账本之前数据的隔离性。所以在使用 Hyperledger Fabric 之前，必须先安装、构建网络所需的基本组件。

安装、构建 Hyperledger Fabric 网络组件有两种方法。

1. 方法一

    手动安装、构建 Hyperledger Fabric 网络组件。通过从 Github 下载源码的方式，手动编译、生成所需的可执行二进制工具和 docker iamges。

2. 方法二

    运行自动化脚本文件 `bootstrap.sh` 安装、构建 Hyperledger Fabric 网络组件，同时脚本会下载 `fabric-samples` 测试用例。运行测试用例中 `byfn.sh` 自动化脚本文件将会构建一个简易的 Hyperleder Fabric 网络环境并引导启动，同时自动生成相应的一些配置文件，可用于测试环境是否正常。

本实训我们将使用方法二来安装、构建 Hyperledger Fabric 网络组件。同时使用 `byfn.sh` 自动化脚本文件检验测试环境。

#####下载 Hyperledger Fabric 网络组件

1. 下载 Hyperledger Fabric 源码

    Hyperledger Fabric 托管在 Github 上的仓库地址：[https://github.com/hyperledger/fabric](https://github.com/hyperledger/fabric)

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbnlsvr0ghj31dz0yxq9o.jpg)

    ```shell
    # 下载源码
    git clone https://github.com/hyperledger/fabric.git
    ```

    > 命令行工具中源码已下载完成，位于 `/opt/go/src/github.com/hyperledger/` 路径中

2. 下载 `fabric-samples` 和 `hyperledger/fabric images` 镜像组

    首先切换路径到 `./scripts` 目录

    ```shell
    cd /opt/go/src/github.com/hyperledger/fabric/scripts
    ```

    执行自动化脚本下载所需的组件，下载版本为 `1.4.0`

    ```shell
    bash ./bootstrap.sh 1.4.0
    ```

    >下载将会非常慢，因为需要下载多达 6G 的镜像和 `fabric-samples` 测试用例。所以命令行工具中已将镜像包和测试用例下载完成，分别位于：`/opt/hyperledger-fabric-images` 和 `/opt/go/src/github.com/hyperledger/fabric/scripts`。

    此时需要做的是将镜像包导入 `docker` 中，执行如下命令（期间需要运行花费大约5分钟）：

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
    ```

    命令执行完后，运行 `docker images` 将会显示如下内容：

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbnndvwql4j311j08etaf.jpg)

3. 提供脚本可执行权限并设置 docker-compose up 的时间

    实训提供的命令行工具中 `shell` 脚本默认没有执行权限，需要手动设置或添加 `bash` 命令

    ```shell
    # 设置脚本权限
    chmod -R 755 /opt/go/src/github.com/hyperledger/fabric/scripts/fabric-samples/bin/
    chmod -R 755 /opt/go/src/github.com/hyperledger/fabric/scripts/fabric-samples/first-network/scripts/
    ```

    由于 `docker-compose up` 有默认 60 s 的时间限制。如果 docker imgaes 群在 60 s 内没有启动成功则会报错。考虑到实训提供的命令行工具配置比较低，需要将手动将 `docker-compose up` 时间限制设置更大。

    ```shell
    # 添加环境变量
    echo "export COMPOSE_HTTP_TIMEOUT=500" >> /etc/profile
    echo "export DOCKER_CLIENT_TIMEOUT=500" >> /etc/profile
    # 激活
    source /etc/profile
    ```

#####测试 Hyperledger Fabric 网络环境

1. 生成证书和密钥

    执行 `byfn.sh` 自动化脚本文件，为各种 Hyperledger Fabric 网络实体生成所有证书和密钥，并且可以实现引导服务启动及配置通道所需的一系列配置文件

    ```shell
    # 切换路径
    cd /opt/go/src/github.com/hyperledger/fabric/scripts/fabric-samples/first-network
    # 执行脚本
    bash ./byfn.sh generate
    ```

    命令成功执行后会生成 1 个 Orderer + 4 个 Peer + 1 个 CLI 组件的网络结构，4 个 Peer 包含在 2 个 Org 中。

    根据提示输入 y，之后终端输出类似如下的日志内容：

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbnnhgg7o2j31b50e740b.jpg)

    ......

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbnnjhk8h2j316d0dqju3.jpg)

2. 启动测试网络

    生成网络所需的证书和密钥后，需要启动网络来确认 Hyperledger Fabric 环境是否能够正常工作。使用 `byfn.sh` 脚本来实现网络的启动，命令如下（期间需要运行花费大约 9 分钟）：

    ```shell
    bash ./byfn.sh up
    ```

    根据提示输入 y，如果终端输出类似如下的日志内容，则代表网络启动且测试功能。

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbnnn9vgr5j31690r3td8.jpg)

    ...

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbnnplv8azj312108lq38.jpg)

3. 关闭测试网络

    网络测试成功后，为了方便后期的操作，最好将其关闭，以防止后期启动网络时造成的冲突错误。关闭网络会将容器关闭，同时删除加密文件。关闭网络可执行如下命令：

    ```shell
    bash ./byfn.sh down
    ```

    根据提示输入 y，之后终端输出类似如下的日志内容（期间大约需要运行 90 s）：

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbnnrgw5pyj31n90u0tfu.jpg)

####编程要求

在右侧命令行中完成测试 Hyperledger Fabric 网络环境，然后点击评测即可。检测 `docker images` 是否正确。

---
开始你的任务吧，祝你成功！