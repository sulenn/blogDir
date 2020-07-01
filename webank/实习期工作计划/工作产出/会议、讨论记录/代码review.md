[toc]

# 代码 review

## 2020.7.1 10:00-11:10 review 杰哥 vrf-rPBFT 代码

1. VRFBasedrPBFTSealer

   - 修改 generateAndSealerRotatingTx 函数名，改为generateTransactionForRotating()

   - 异常嵌套问题

2. VRFBasedrPBFTEngine :

   - 修改 forwardRemaingTxs 函数名，考虑如何命名比较合适

   - 156 行 epochSize 变量名需要调整

   - 精简某一个函数的代码

3. WorkingSealerManagerLmpL.cpp

   - 110行附近，remove 节点问题，不删节点只加节点

   - 上一个区块的 hash 值作为 vrf 的入值有没有问题？

   - 280 行附近需要整理代码处理逻辑顺序。

   - calNodeRotatingInfo 函数中添加一个else if 判断条件，workingSealer 数量和 Sealer 数目一致就变为 0。

   - selectNodesFromList 函数中 shuffle 算法是否可用标准库中提供的？

4. vrf_rPBFT 不需要作为一个独立共识算法说明，vrf 可以作为 rBPFT 的一个特性。另外 supported_version、块高切换共识算法方案啥的。