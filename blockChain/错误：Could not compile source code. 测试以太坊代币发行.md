***出错***：粘贴`https://www.ethereum.org/token#the-code`中代码至Ethereum Wallet时报错
```
 Could not compile source code. 
 Defining constructors as functions with the same name as the contract is deprecated. Use "constructor(...) { ... }" instead.
    function TokenERC20(
    ^ (Relevant source part starts here and spans across multiple lines).
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181129211726484.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FpdWJpbmdjc2Ru,size_16,color_FFFFFF,t_70)***出错原因***：solidity版本更新，其中合约中构造函数定义的方法发生了变化，具体可参考`https://solidity.readthedocs.io/en/v0.4.24/contracts.html#creating-contracts`
***修改***：将代码中`function TokenERC20`改成`constructor`即可
***以太坊模拟发行代币可参考***：`https://www.jianshu.com/p/410198814516`和`https://ethfans.org/topics/118`
***模拟代币发行对网络有一定要求***