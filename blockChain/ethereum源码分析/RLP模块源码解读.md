# RLP模块源码解读

rlp(递归长度前缀，Recursive Length Prefix)

以太坊的rlp主要分为：`字节数组的序列化` 以及 `树形结构的数据序列化`

字节数组和树形结构都包含两个部分：`编码` 和 `解码`

## 1. 字节数组的序列化

### 1.1. 编码部分



**参考**：

- [以太坊源码解读-第2讲-rlp模块源码解读](http://www.wjblog.top/articles/c2a19e0e/)