[toc]

# 第二个五人日-熟悉FISCO BCOS

## 第一天

- 重装 WSL ubuntu18.04，成功编译 master 的 release-2.5.0分支。其中遇到了几个错，需要手动安装 `zlib1g-dev`、`libgmp-dev`、`flex`和`bison`包。

- 阅读**用户手册 - 国内镜像和cdn加速攻略 - 文档**：可以下载的工具、代码和阅读的文档有，FISCO BCOS源码与二进制程序、FISCO BCOS文档和FISCO BCOS配套工具。其中二进制程序和配套工具可以不同程度的通过 github、gitee和官网 cdn 加速获得。文档阅读的话有外网和内网两个不同的网址查看。
- win10编译 go-sdk 中的 /cmd/console.go，调用console.exe 支持的命令查看 WSL 中已经启动单群组四节点网络信息。