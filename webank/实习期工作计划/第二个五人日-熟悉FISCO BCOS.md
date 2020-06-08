[toc]

# 第二个五人日-熟悉FISCO BCOS

## 第一天

- 重装 WSL ubuntu18.04，成功编译 master 的 release-2.5.0分支。其中遇到了几个错，需要手动安装 `zlib1g-dev`、`libgmp-dev`、`flex`和`bison`包。
- 阅读**用户手册 - 国内镜像和cdn加速攻略 - 文档**：可以下载的工具、代码和阅读的文档有，FISCO BCOS源码与二进制程序、FISCO BCOS文档和FISCO BCOS配套工具。其中二进制程序和配套工具可以不同程度的通过 github、gitee和官网 cdn 加速获得。文档阅读的话有外网和内网两个不同的网址查看。
- win10编译 go-sdk 中的 /cmd/console.go，调用console.exe 支持的命令查看 WSL 中已经启动单群组四节点网络信息。

## 第二天

- 在新装的 ubuntu 18 中复现上个星期的工作：通过 build_chain.sh 脚本生成节点文件，启动单群组四节点，启动 console，查询单群组信息，部署合约，调用合约。编译 FISCO BCOS MASTER 和 Release-2.5.0 分支。
- 执行 go-sdk readme 中的内容：分国密和非国密两种。下载 solc；编译生成 abigen；使用 solc 编译 solc 、生成 abi和bin 文件；使用 abigen 工具将 abi、bin 转换为 go 文件。创建 main 函数，部署合约、加载智能合约并调用查询接口、调用智能合约写接口

- 开始阅读 go-sdk 源码：

  - `config`文件夹 - `config.go`：使用第三方 viper 包，加载配置文件信息。如加载 `config.toml` 配置文件
  - `config`文件夹 - `parsepem.go`：解析 `pem` 私钥文件。如解析 `./ci/0x83309d045a19c44dc3722d15a6abd472f95866ac.pem` 私钥证书
  - `client`文件夹 - `goclient.go`：根据指定的 `nodeURL` 和 `gourpID` 连接客户端

- 提两个 pr：

  - FISCO-BCOS**：[地址](https://github.com/FISCO-BCOS/FISCO-BCOS-DOC/pull/851)

    1. 修改“区块同步优化”文档 “区块状态树状广播”小节中表述不当的公式；

    2. 修改 “国内镜像和CDN加速攻略”文档中的错别字："和入" 改为 "合入"；

    3. 删除“国密支持”文档中不恰当且多余的内容：通过命令行下载 build_chain.sh 工具并赋予可执行权限之后，./fisco 文件夹中只有 build_chain.sh 这一个文件。并没有/bin/fisco-bcos 这个文件。所以干脆将展示 ./fisco 文件夹结构删除。

  - **go-sdk**：[地址](https://github.com/FISCO-BCOS/go-sdk/pull/21)

    1. 修改多余文字描述：当编译好 abigen 工具之后，不需要移动到 ./store 文件夹中

    2. 增加关于“国密区块链平台搭建”的提示：因为默认的操作都是在非国密的操作下完成，但是在合约编译、部署的部分提供了国密的操作命令。所以感觉有必要在合约编译的文档部分稍稍提醒一下。