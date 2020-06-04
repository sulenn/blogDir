[toc]

# 第一个五人日-熟悉FISCO BCOS

**任务**：安装、编译、使用console部署与调用合约、阅读公众号文章

**输出**：体验报告、改进建议

## 第一天

- 入职报到、填写入职材料、领取入职设备、配置电脑锁、公司密码等
- 配置电脑环境：包括但不限于 WSL、golang、FISCO BCOS、Java
- 安装各种软件：包括但不限于 goland、vscode、typora、postman、SSTap-beta、QQ、微信、百度网盘、有道云笔记
- ***在 WSL 中搭建单群组 FISCO BCOS 联盟链：使用 build_chain.sh 脚本搭建4节点单群组联盟链，通过命令行查看进程、日志；使用 console 部署与调用合约；查看区块链信息等。参考 FISCO BCOS 官方文档：[安装](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/installation.html)***

## 第二天

- 阅读公众号文章 - **FISCO BCOS快速建链实现之路**：介绍 `build_chain.sh` 开发部署工具的背景、功能、优点，`build_chain.sh` 开发部署工具可用于检查环境、解析参数、获取 FISCO BCOS 可执行程序、生成私钥证书、生成配置文件和工具脚本等。 

- 阅读**证书说明文档**：使用 `x509 协议的证书格式`，默认采用三级的证书结构，包括四类证书，分别是：链证书、机构证书、节点证书、SDK证书。如果证书过期的话，需要严格按照步骤续期证书（通过私钥生成证书请求文件，上级通过自己的私钥和证书以及下级的证书请求文件为下级签发新的证书）。

- 针对**证书说明文档**中可能存在的问题，提了两个 pr

  - 第一个pr主要用于改正错别字和添加标点符号，[pr地址](https://github.com/FISCO-BCOS/FISCO-BCOS-DOC/pull/833)：
    	1. 将 `docs/manual/certificates.md` 中的 "入下“ 修改为 ”如下“；
     	2. 为 `docs/manual/certificates.md` 中的某一行添加标点符号 ”，“

  - 第二 pr 主要用于补充、完善文档内容，[pr地址](https://github.com/FISCO-BCOS/FISCO-BCOS-DOC/pull/839)：
     	1. 修改 生成节点/SDK证书 小节中的文字描述；
        	2. 将 机构证书/链证书续期 小节标题调整为 四类证书续期简易流程。因为 ”链证书“ 过期之后需要重新生成四类证书；
           	3. 补充 四类证书续期简易流程 小节中对 机构证书、节点证书、sdk证书 等内容的步骤说明。

- 了解并实验 pr 合并、更新的流程：base repo 合并 pr或推送新的内容之后，需要使用 fetch、rebase 命令更新本地仓库，因为 squash and merge pr 之后会生成新的 commit sha 值，该 commit sha 值不同于 pr 提交的 commit sha 值。因为需要实时更新本地仓库。不然下一次提新 pr 时，compare 会出现多个 commit 信息。 

## 第三天

- 阅读**多群组部署**文档：主要包括两种多群组建网方式
  1. 星形拓扑多群组网络：中心机构节点属于多个群组，其它机构节点属于单个群组
  2. 并行多群组网络：每个节点属于多个群组

- 阅读公众号文章 - **如何为FISCO BCOS做贡献**：该文章介绍了三部分内容，分别是：提 issue、通过网页修改文档和通过 fork repo 提交 Pr
- 阅读**获取可执行程序**文档：介绍了三种获取二进制预编译可执行文件的方式，分别是：下载静态链接的预编译程序，[下载地址](https://github.com/FISCO-BCOS/FISCO-BCOS/releases)、下载 docker 镜像，[下载地址](https://hub.docker.com/r/fiscoorg/fiscobcos/tags)、和源码编译
- 阅读并测试**组员管理**文档：FISCO BCOS 存在三种类型的节点，分别是：游离节点、观察者节点和共识节点。该文档中还讲解了新组员入网、入群组和退群组、退网的过程。最后用一个 Demo 进行详细的讲解。
- 阅读**CA 黑白名单介绍**文档：**CA黑名单** - 别称**证书拒绝列表**（certificate blacklist，简称CBL）、**CA白名单** - 别称**证书接受列表**（certificate whitelist，简称CAL）

- 阅读**日志说明**文档：内容有，日志格式、常见日志说明和日志模块关键字
- 针对文档中出现的问题，提了一个 Pr，[地址](https://github.com/FISCO-BCOS/FISCO-BCOS-DOC/pull/841)
  1. 修改 `docs/manual/log_description.md` 中文字语法错误：将文字 `...群组日志都输出log目录下到...` 修改为 `...群组日志都输出到log目录下...`
  2. 删除 `docs/manual/node_management.md` 中多余的操作：`mkdir -p ~/fisco && cd ~/fisco` 该条操作命令对下面的操作没有任何作用，`mkdir -p ~/fisco` 甚至会报错
  3. 删除 `docs/manual/node_management.md` 多余的描述：拷贝 `node0/config.ini` 文件之后，应该不需要修改 `listen_ip`，因为 `listen_ip` 的值为 `127.0.0.1`，修改前后一致。