[toc]

# 第二个五人日-熟悉FISCO BCOS

**任务**：编译、部署2.5版本，执行Go-SDK的readme，部署与调用合约，分国密与非国密、开始尝试阅读 go-sdk 源码

**输出**：体验报告、改进建议

## 本周工作总结

1. 继续熟悉公司环境、向各位优秀的师兄师姐学习
2. 阅读 FISCO-BCOS 、go-sdk readme 以及 KM 文档，针对文档中可能存在的问题提了两个 pr（已合并）。
3. 编译最新 Master - 2.5.0 分支，通过 build_chain.sh -e 参数指定 fisco-bcos 二进制脚本路径，通过 -g 参数运行国密区块链。
4. 测试 go-sdk readme demo 案例，包括使用国密和非国密部署和调用合约
5. 阅读源码，通过运行 demo 和 test 测试用例，以打断点的方式了解 go-sdk api 、console 控制台功能逻辑流转的过程

## 下周工作计划

1. 继续向各位优秀的师兄师姐学习
2. 阅读源码和 FISCO-BCOS 文档
3.   编写Go-SDK各模块功能描述、在readthedoc上补充Go-SDK文档，包括配置说明、模块说明、接口说明、HelloWorld 以及TableTest使用 demo 案例等。

## 第一天

- 重装 WSL ubuntu18.04，成功编译 master 的 release-2.5.0分支。其中遇到了几个错，需要手动安装 `zlib1g-dev`、`libgmp-dev`、`flex`和`bison`包。
- 阅读**用户手册 - 国内镜像和cdn加速攻略 - 文档**：可以下载的工具、代码和阅读的文档有，FISCO BCOS源码与二进制程序、FISCO BCOS文档和FISCO BCOS配套工具。其中二进制程序和配套工具可以不同程度的通过 github、gitee和官网 cdn 加速获得。文档阅读的话有外网和内网两个不同的网址查看。
- win10编译 go-sdk 中的 /cmd/console.go，调用console.exe 支持的命令查看 WSL 中已经启动单群组四节点网络信息。

## 第二天

- 在新装的 ubuntu 18 中复现上个星期的工作：通过 build_chain.sh 脚本生成节点文件，启动单群组四节点，启动 console，查询单群组信息，部署合约，调用合约。编译 FISCO BCOS MASTER 和 Release-2.5.0 分支。
- 执行 go-sdk readme 中的内容：分国密和非国密两种。下载 solc；编译生成 abigen；使用 solc 编译 合约、生成 abi和bin 文件；使用 abigen 工具将 abi、bin 转换为 go 文件。创建 main 函数，部署合约、加载智能合约并调用查询接口、调用智能合约写接口

- 开始阅读 go-sdk 源码：

  - `config`文件夹 - `config.go`：使用第三方 viper 包，加载配置文件信息。如加载 `config.toml` 配置文件
  - `config`文件夹 - `parsepem.go`：解析 `pem` 私钥文件。如解析 `./ci/0x83309d045a19c44dc3722d15a6abd472f95866ac.pem` 私钥证书
  - `client`文件夹 - `goclient.go`：根据指定的 `nodeURL` 和 `gourpID` 连接客户端

- 提两个 pr：

  - FISCO-BCOS**：[地址](https://github.com/FISCO-BCOS/FISCO-BCOS-DOC/pull/851)

    1. 修改“区块同步优化”文档 “区块状态树状广播”小节中表述不当的公式；

    2. 修改 “国内镜像和CDN加速攻略”文档中的错别字："和入" 改为 "合入"；

    3. 删除“国密支持”文档中不恰当且多余的内容：通过命令行下载 build_chain.sh 工具并赋予可执行权限之后，./fisco 文件夹中只有 build_chain.sh 这一个文件。并没有/bin/fisco-bcos 这个文件。所以干脆将展示 ./fisco 文件夹结构删除。

  - **go-sdk**：[地址](https://github.com/FISCO-BCOS/go-sdk/pull/22)
1. 修改多余文字描述：当编译好 abigen 工具之后，不需要移动到 ./store 文件夹中
   

## 第三天

- 继续尝试阅读源码：

  - `smcrypto`文件夹 - `sm2.go`：支持国密 `sm2` 签名。只有 `SM2Sign` 和 `SM2PreProcess` 两个 function
  - `smcrypto`文件夹 - `smcrypto.go`：sm2 国密算法实现。包括解析私钥，从解析后的私钥获得公钥、地址和 `pem` 值以及签名（调用 `sm2.go`）等
  - `smcrypto`文件夹 - `smcrypto_test.go`：测试 `smcrypto.go` 实现的 `sm2` 算法功能

  - `sm3`文件夹 - `sm3.go`：sm3 国密 Hash 算法实现。32 byte 加密输出
  - `sm3`文件夹 - `sm3_test`：测试和压测 `sm3.go` 中实现的 Hash 算法

- 了解SM国密算法：国产密码算法目前主要公开的有SM2、SM3、SM4三类算法，分别对应非对称算法、哈希算法和对称算法。
  - **sm2**：**私钥 33 byte 长**、**公钥 64 byte 长**、**地址 20 byte 长**
  - **SM3加密的 Hash 值长：32 byte**
- 了解secp256k1椭圆曲线加密算法：Secp256k1是ECDSA(椭圆曲线数字签名算法)曲线的参数，公/私钥就对应于该曲线上的点。
  - **私钥长 32 byte 长**

## 第四天

- 阅读源码：主要阅读 `goclient_test.go` 测试文件。通过 debug 打断电、跳入、跳出的方式来了解、熟悉源码。
- 编译 master（2.5.0）源码：因为 master 分支更新了，而且我之前使用 build_chain.sh 脚本时没有用过 -e 参数指定 fisco-bcos 版本。

## 第五天

- 使用 fisco-bcos master 2.5.0 创建国密应用：使用 fisco-bcos master 2.5.0 创建国密单群组四节点区块链网络，通过 console 查看区块链信息，同时部署、调用合约

- 了解 FISCO BCOS 中 gas 的用途：gas 不用于交易，只用于判断智能合约执行是否出现死循环
- 国密双证书：国密在加密、签名时需使用双证书，gmennode.crt、gmennode.key、gmnode.crt、gmnode.key 就是双证书。gmnode.serial 文件用于申请证书时使用的序列号。

- channel 和 jsonrpc 的区别：两种不同的通信方式。jsonrpc 是 http 通信，不需要加密。channel 是 https 通信，需要使用证书。
- 阅读文档 - 运维部署工具：[FISCO BCOS generator](https://github.com/FISCO-BCOS/generator) 主要供企业级用户使用，提供了部署、管理和监控多机构多群组联盟链等功能。可以降低机构间生成与维护区块链的复杂度、提供更高的节点安全性、保障机构间节点的对等性

- 阅读文档 - 使用手册 - 控制台：控制台是交互式客户端工具，通过 Web3SDK 与区块链节点建立连接，实现对区块链节点数据的读写访问请求。控制台支持查询区块链状态、管理区块链节点、部署并调用合约等功能。此外，控制台还有合约编译工具，可以快速方便的将 Solidity 合约文件编译为 Java 合约文件。

- 使用 build_chain.sh 工具构建国密和非国密应用是操作的区别：国密在使用 build_chain.sh 创建节点配置文件的时候，需要指定 -g 参数。在配置控制台 console 时需要修改 applicationContext-sample.xml 配置文件，手动打开国密开关。还需要替换 console 中的国密编译包（**编译智能合约、生成国密版 java 合约文件**）。