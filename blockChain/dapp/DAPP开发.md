# DAPP开发

## 1. 《永存的留言》

- 在线体验(Ludis)：[http://words.ldsun.com/](http://words.ldsun.com/)

- 参考至：
    [https://github.com/mokeychan/etherumForeverWords](https://github.com/mokeychan/etherumForeverWords)
    [https://blog.csdn.net/qq_33764491/article/details/80570266](https://blog.csdn.net/qq_33764491/article/details/80570266)

==注意==：运行该 `dapp` 有两种方法

1. 从 `github` `clone` 项目之后，项目本身已经带有编译生成的文件，不需要再次编辑。此时只需要将 `SimpleStorage.sol` 文件内容拷贝进 `remix` 进行部署，获得对应的合约地址即可。

2. 从 `github` `clone` 项目之后，手动修改`SimpleStorage.sol` 文件内容为 `solidity 0.5.0` 支持的语法版本（这里要求本地的 `truffle` 版本为 0.5.0）。然后即可用 `truffle compile` 本地编译 `solidity` 代码。对应`remix` 部署的代码也为更新之后支持 `solidity 0.5.0` 语法的代码。

## 2. 宠物商店

- 参考至：
    [https://truffleframework.com/tutorials/pet-shop](https://truffleframework.com/tutorials/pet-shop)

注意网络问题，可能需要开启代理才能使用

==注意==：truffle版本为 `5.0.0`，不然可能会出错。[https://github.com/trufflesuite/truffle/issues/1727](https://github.com/trufflesuite/truffle/issues/1727)

==注意==：运行 `npm run dev` 命令时需要连接外网。因为网页中请求的 `jquery.min.js` 需要翻墙才能加载

![2](http://ww1.sinaimg.cn/large/006alGmrgy1g0gjl9chbjj30ro0mb781.jpg)
