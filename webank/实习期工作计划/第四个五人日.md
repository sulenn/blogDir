[toc]

# 第四个五人日

## 本周工作计划

1. 继续向各位优秀的师兄师姐学习
2. FISCO BCOS 对 ipv6 全链路支持，包括至少一个 sdk
3. rocksdb storage 工具使用说明文档
4. go-sdk 文档部分编写

## 本周工作总结

1. 继续向各位优秀的师兄师姐学习
2. 完成 rocksdb-storage 文档编写并在 wiki 上传提测文档
3. 完成 ipv6 本地的适配测试，编写 ipv6 提测文档并上传 wiki
4. 针对 FISCO BCOS、console、web3sdk 和 FISCO BCOS DOC 各提一个 PR 

## 下周工作计划

1. 完成 ipv6 远程测试机适配测试
2. 完成 go-sdk 文档编写
3. 完成 go-sdk 命令行代码补全

## 第一天

1. 完成 rocksdb-storage 文档编写

2. 提了一个 pr，地址为：[https://github.com/FISCO-BCOS/FISCO-BCOS/pull/1666](https://github.com/FISCO-BCOS/FISCO-BCOS/pull/1666)
3. 完成本地 go-sdk 对 ipv6 的适配测试

##　第二天

1. 修改 web3sdk 对 ipv6 的适配性
2. 测试 fisco bcos 配置文件 config.ini 中 p2p node url 对 host 域名的解析能力
3. 编写部分 ipv6 文件

## 第三天

1. 针对 console repo 提了一个 pr：**地址**：[https://github.com/FISCO-BCOS/console/pull/276](https://github.com/FISCO-BCOS/console/pull/276)、**解决的问题**：Fix the problem that download_console.sh can't get the latest release version

2. wiki 上提交 ipv6 提测文档

## 第四天

1. 测试 rocksdb 存储模式下开启落盘加密的 recover 情况，分国密和非国密：测试顺利完成

2. 完善 rocksdb-storage 文档：增加 -e 选项，并增加部分文件说明

3. 针对 web3sdk 解析、适配 ipv6 ，提 pr，地址为：https://github.com/FISCO-BCOS/web3sdk/pull/668

4. 针对 fisco bcos 文档，提 Pr，地址为：https://github.com/FISCO-BCOS/FISCO-BCOS-DOC/pull/876