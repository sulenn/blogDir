# web3js相关问题

## 1. web3.isConnected is not a function

![2](http://ww1.sinaimg.cn/large/006alGmrly1g2aadiav8kj30ji07l3zv.jpg)

**原因**：`web3` 版本有问题

**解决方法**：用 `npm` 指令重新下载最新的版本，本次使用版本为 `0.20.0`

**参考**：[TypeError: web3.isConnected is not a function的解决方法](https://blog.csdn.net/baidu_38370610/article/details/86590983)

---

## 2. BigNumber Error: new BigNumber() not a base 16 number

![2](http://ww1.sinaimg.cn/large/006alGmrly1g2nxiomwgqj30v80c4ad7.jpg)

**可能存在的原因**：

- 节点没有同步完成

- 当指向一个实际没有实现该API的地址时，也会发生同样的事情。如果回退函数不返回字符串，则解析字符串时会遇到麻烦。

- 如果重新修改部署过合约，请检查代码中的合约地址是否对应修改了

- 合约是否已经被确认

**参考**：[【区块链】记录合约开发中遇到的坑](https://blog.csdn.net/loy_184548/article/details/79746034)

---

## 3. ==问题==  BigNumber Error: new BigNumber() not a base 16 number

solc 0.5.7 编译器版本对我们的给出的示例代码，在部署之后调用会出现如下问题： `BigNumber Error: new BigNumber() not a base 16 number:`

![w](http://ww1.sinaimg.cn/large/006alGmrly1g2jnjoq7elj31960m2tbt.jpg)

**解决方法**：将编译器版本修改为 `0.5.1` 即可