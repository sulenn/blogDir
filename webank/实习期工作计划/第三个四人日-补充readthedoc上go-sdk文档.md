# 第三个四人日-补充readthedoc上go-sdk文档.md

## 本周工作计划

1. 继续向各位优秀的师兄师姐学习
2. 阅读源码和 FISCO-BCOS 文档
3. 编写Go-SDK各模块功能描述、在readthedoc上补充Go-SDK文档，包括配置说明、模块说明、接口说明、HelloWorld 以及TableTest使用 demo 案例等。
4. 测试 FISCO-BCOS 对 ipv6 的支持情况 
5. pbft 共识节点不足4个后如何 recover

##　第一天

- 阅读文章 - KM - 区块链防重放方案，nonce，randomid，blocklimit：解决区块链中重放攻击的方案，通过添加 blocklimit 和 randomid ，引入“交易处理窗口”来限制一笔交易打包的范围期限。
- 完成go-sdk配置说明文档编写
- 完成部分go-sdk api文档编写

## 第二天

- 在小白师兄的协助下，本地测试 FISCO BCOS 对 ipv6 的适配支持情况
- 完成 go-sdk 测试 HelloWorld.sol 和 Table.test 的案例
- 编写 HelloWorld 合约样本案例文档

## 第三天

- 完成本地单群组四节点（mysql存储）recover测试
- 编写 Store 合约样本案例文档

## 第四天

- 补测 recover 网络之后，新扩容共识节点的情况
- 完成 `recover` 方案文档编写，只针对 mysql 存储
- 初步完成 `IPv6` 文档编写
- 阅读 go-sdk 源码