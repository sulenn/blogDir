# CKshare：secured cloud-based knowledge-sharing blockchain for injection mold redesign

## ABSTRACT

CKshare：一个可信的摸具再设计知识共享平台，基于私有云和区块链技术。首先：使用私有云存储满足自己私有和数据格式的磨具再设计知识。第二：使用区块链网络记录知识和交易来确保安全和可信。第三：基于 k-nearest neighbors的数据检索机制。

## 1. Introduction

摸具设计是注塑摸具项目中影响摸具质量、交付和花费的重要因素。
模具再设计是模具设计中必有的阶段，它会在参数、材料和过程等方面对前一个设计进行修改。目的是解决模具实验中存在的遗留问题，确保最合适的知识和技术应用到再设计过程。（这里会举例前人在解决模具再设计问题中的方案）
已有的知识共享平台只适合拥有大量资源的单个、大型企业，不适合中小型企业（SMEs）。因此，当前可用的共享平台不适用模具工业，因为该工业由许多小型和中型公司组成。（接下来继续提出现实中存在的问题，并列出一些前人针对这些问题的解决工作。然后继续说这些工作中存在的问题）

## 2. Literature review

模具工业、私有云和区块链中知识共享概述

### 2.1 Knowledge-sharing in the mold industry

学术界中知识共享存在多种不同定义，由于模具工业中互操作的特性，本文只考虑团队和组织之间的知识共享交换或交易。

举例 6 个前人在模具工业中针对知识共享所做的贡献

对比六个用于知识共享的系统，列表展示

分析列表中六个系统

通常的知识共享不同于模具工业中的知识共享。下面讲解一般的知识共享特性，列出三点模具中的不同特征。

当前不存在一个企业间安全的知识共享平台

### 2.2 Private cloud

私有云是云计算的一种模式。和共有云相比，它更可控、更安全以及更加隐私

举两个前人的例子，说明私有云的优点

继续阐述私有云的优点

简单点名私有云在本文中应用的好处，说明一些缺点，引出区块链可以解决这些缺点。

### 2.3 Blockchain

举例中本聪 bitcoin，区块链应用的开始。介绍区块链基本机制，引用、举例说明区块链的优点。

区块链处理应用到金融领域，还可以应用到其它很多领域。引用论文、案例说明。

区块链契合本文的应用场景。列出三条优点。

继续说明当前已有应用中存在的问题：1. 数据专一，无法扩展和兼容。需要统一数据格式。 2. 没有激励机制来刺激模具企业分享知识 3. 安全问题。知识共享系统中用户激增后，知识所有权、权限控制等问题如何解决。

## 3. The architecture of the proposed ckshare platform

CKshare 系统体系结构

![2](https://ws1.sinaimg.cn/large/006alGmrly1g6fcxylzcvj30xs0xoqr3.jpg)

四个层次：enterprise layer,knowledge resource layer,blockchain network and application layer

企业层用户包括 injection mold product(IMP) designers, mold designers and mold manufactures。由企业层提供的数据和信息将在知识资源层存储和处理。

知识资源层，使用私有云存储每个组织的应用知识。私有云可以对每个组织提供安全和可控的管理方法。

区块链网络提供安全和可信赖的知识共享网络。本文定义两层网络（遵循侧链规则），知识链（KBC）和交易链（TBC）。

应用层负责提供不同应用接口和相关联的终端设备。

## 4. Key mechanisms of the ckshare platform

CKshare 系统针对知识共享的机制。私有云、KBC创建、基于KNN查询以及TBC创建机制。

### 4.1 private cloud

### 4.2 Knowledge blockchain creation

![2](https://ws1.sinaimg.cn/large/006alGmrly1g6fcvn3xp2j31fl0ymnep.jpg)

![2](https://ws1.sinaimg.cn/large/006alGmrly1g6fcwsdhbej31f10ynh7w.jpg)

### 4.3 Knn-based inquiry

### 4.4 Transaction blockchain creation

## 5. Case study

案例研究，证明该系统的有效性

找现实中存在的中型模具公司进行案例研究。解释为什么选这个中型公司。
解释 glove box 手套盒子。
提出中小型模具企业中存在的几个问题。引出该系统存在的必要性。

### 5.1 Prototype implementation of ckshare

区块链选择以太坊。简单解释以太坊。用HTML和JavaScript写了应用，连接本地区块链。用智能合约写了去中心化应用，编译和部署在本地区块链
四个依赖。1：node.js 包管理工具、2：Truffle框架，基于区块链编写区中心化应用、3：Ganache管理本地区块链、4：Meta Mask插件，用于连接区块链网络

## 6. Conclusion

本文基于区块链和私有云提出一个分布式和安全的 CKshare 平台，该平台用于有关注塑模具再设计的知识共享。私有云为每个组织提供安全数据存储。两种区块链，KBC用于记录每个组织的模具在设计知识。TBC记录交易，知识所属权，提供知识共享获利机制。KNN用于提高检索效率。
未来待解决的几个问题：

1. 缺少共识机制对比

2. 缺少标签，无法区分虚假数据

3. 系统扩展，应用至模具再设计之外的领域
