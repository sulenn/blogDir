# 错误：Could not compile source code. 测试以太坊代币发行

## 错误1

**出错**：粘贴 [https://www.ethereum.org/token#the-code](https://www.ethereum.org/token#the-code) 中代码至Ethereum Wallet时报错

```no
 Could not compile source code. 
 Defining constructors as functions with the same name as the contract is deprecated. Use "constructor(...) { ... }" instead.
    function TokenERC20(
    ^ (Relevant source part starts here and spans across multiple lines).
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20181129211726484.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FpdWJpbmdjc2Ru,size_16,color_FFFFFF,t_70)

**出错原因**：solidity版本更新，其中合约中构造函数定义的方法发生了变化，具体可参考[https://solidity.readthedocs.io/en/v0.4.24/contracts.html#creating-contracts](https://solidity.readthedocs.io/en/v0.4.24/contracts.html#creating-contracts)

**修改**：将代码中`function TokenERC20`改成`constructor`即可

**以太坊模拟发行代币可参考**：
[https://www.jianshu.com/p/410198814516](https://www.jianshu.com/p/410198814516)

**模拟代币发行对网络有一定要求**

## 错误2

**出错**：粘贴 [https://www.ethereum.org/token#the-code](https://www.ethereum.org/token#the-code) 中代码至Ethereum Wallet时报错

```no
Could not compile source code. 

 Expected ',' but got identifier
    function receiveApproval(address _from, uint256 _value, address _token, bytes calldata _extraData) external;
```

![3](http://ww1.sinaimg.cn/large/006alGmrly1fzin7vopx6j30zo0e20v4.jpg)

**修改**：参考图片
