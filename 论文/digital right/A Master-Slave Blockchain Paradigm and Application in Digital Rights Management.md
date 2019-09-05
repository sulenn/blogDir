# A Master-Slave Blockchain Paradigm and Application in Digital Rights Management

==本篇文章，主要讲的是主从区块链架构。数字版权管理只是其中的一个应用。并且只用了一章，较少篇幅来描述，基本上没有对数字版权进行讲解==

## Abstract

针对当前区块链平台存在的问题：笨重、账本容量大、同步费时，本文提出新的主从区块链范式用于适普计算，适用于一般PC、移动设备或平板电脑，可用于挖矿和验证。我们将传统区块链模型划分为两层，主节点层和一系列从代理层。我们为 partially computing model（PCM）和 non-computing of model（NCM）设计了两种方法。最后，大量模拟效果表明主从区块链模式是可行且可扩展，尤其适用于 5G 环境下，并且能应用于 DRM 相关的应用中

## 1. INTRODUCTION

区块链最初的核心动机是在匿名的环境中构建加密的、去中心化的数字货币系统。当前区块链平台如 bitcoin、ethereum、hyperledger 等主要用于数字货币、去中心化可信数据管理系统或供应链管理。但是，当前区块链平台需要同步大量的账本，主机需要参与计算、验证、同步和存储。所以说当前区块链平台是非常笨重且难扩展的。

介绍一下区块的简单组成。

详细介绍 bitcoin

点一下 bitcoin 的缺点，实时交易的效率不够。引出 ethereum，并详细介绍。

介绍 hyperledger fabric

重新定义了一下区块链，以及应用。举了一个区块链应用的方案。然后指明，由于区块链平台效率问题，导致像手机、平板和个人 pc 等设备无法直接接入平台参与计算、出块、验证等。所以需要设计一种针对区块链的灵活和可扩展的计算范式。

## 2. RELATED WORK

### 2.1 Current popular blockchain technology

#### 2.1.1 Bitcoin and its extendible blockchain platform

#### 2.1.2 Ethereum blockchain an its Token-based Innovation

#### 2.1.3 Hyperledgerfabric consortium blockchain platform

#### 2.1.4 Other new blockchain technologies

### 2.2 Existing problems and flaws of current blockchain

#### 2.2.1 Lacks of extendidle architecture of blockchain

#### 2.2.2 Heavy-weight but low efficiency of TPS

#### 2.2.3 Lack of consensus for new blockchain paradigm

## 3. THE PROPOSED MASTER-SLAVE BLOCKCHAIN SCHEME

## 4. SECURITY INFRASTRUCTURE OF MSB BLOCKCHAIN SCHEME

## 5. MASTER-SLAVE BLOCKCHAIN EXECUTION AND CONSENSUS

## 6. EVALUATION OF MASTER-SLAVE BLOCKCHAIN SCHEME

## 7. ANALYSIS OF MASTER-SLAVE BLOCKCHCIAN SCHEME

## 8. THE MASTER-SLAVE BLOCKCHCIAN APPLICATION IN DRM

## 9. CONCLUSION

本文最大的贡献在于提出了新的主从区块链范式模型用于针对 PC 和移动设备参与挖矿和验证过程的普适计算。提出了区别于传统区块链模型的双层架构。最后，基于 Bitcoin 系统评估了 MSB 架构，大规模模拟证明骑可行性、可扩展性。PeW 和 PoC 共识机制在 DRM 相关应用中的合理性是之后持续研究中的重点。性能和 TPS 提高将是未来研究中最重要的问题。
