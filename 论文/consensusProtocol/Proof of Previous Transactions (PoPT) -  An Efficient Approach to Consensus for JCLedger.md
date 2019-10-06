# Proof of Previous Transactions (PoPT): An Efficient Approach to Consensus for JCLedger

## Abstract

JCLedger 是基于区块链的分布式账本，用于 JointCloud，可以提高不同云之间交换资源的可靠性和便利性。其实现最大的挑战是共识机制。现存的共识机制，如：PoW 和 Pos，需要大量的计算资源，且低吞吐量并伴有垄断的风险。

本文提出基于 PBFT 的共识算法 proof of previous transactions(POPT)，记账人由特殊的 hash 算法从确定数量的候选人中获得。获选人来自参与 JointCloud POPT 共识过程的用户。同时，本文针对并行计算提出了新的区块链结构，用于提高 JCLedger 的吞吐规模。提出一致性共识算法为不同记账人分配交易。模拟实验表明 PoPT 为不同算力的用户提供了同等的记账机会，并行记账可以使 JointCloud 中大规模和高频交易处理更有效。

## 1. INTRODUCTION

JointCloud 是新一代云计算模型，允许多云服务提供商(CSPs)跨云进行合作，促进小中型企业成为云服务顾客（CSCs）。

为了让 JointCloud 中云资源和价值交换更可信和便利，前人提出了 JCLedger，一个基于区块链的分布式账本。

共识算法从所有用户/节点中为每个区块选择记账人。账本/区块链由记账人打包的区块组成。

记账人选择的两条基本原则（为 JCLedger 设计共识算法的核心要素）：

1. 选择过程不能由少数人控制（不超过一半）

2. 选择结果是不可预测的

JointCloud 中参与者包括 cloud services brokers（CSBs），CSCs，和 CSPs。

在满足共识算法的基础需求外，我们需要以下完成以下约束：

1. 避免浪费算力

2. 为不同算力的用户提供同等记账的机会

3. 记账激励更合理

4. 处理大规模和高频交易

之前，我们为 JCLedger 提出过基于 POW 的共识算法 PoPF。本文我们提出另一个共识算法 proof of previous transaction（PoPT），满足以上约束，且更适合 JointCloud。PoPT 基于 PBFT，比 PoPf 更高效。PoPF 使用哈希函数从获选人中选择记账人，用新链结构进行并行记账。

## 2. BACKGROUND AND RELATED WORK

除了满足区块链中共识算法的基本需求外，我们希望共识算法可以使系统更有效。让系统更有效的主要因素是记账人。记账人的工作是打包区块，包括在广播区块前收集和验证交易。交易的验证包括发送人签名的验证，以及是否同其它交易冲突。==区块链系统的效率主要取决于记账交易的处理速度。==

## 3. DESIGN OF POPT

每个区块记账人根据用户的排名选取，排名取决于用户参与前一个交易的情况。

本章节包括：链结构，为每个区块确定区块数量，记账人选取，交易分配，区块打包与验证，基于 PBFT 的共识过程

### 3.1 Chain Structure

![image.png](https://ws1.sinaimg.cn/large/006alGmrly1g7osisze5ij30ht0fy0u8.jpg)

区块链由区块高度组成。不同区块高度可能有不同区块数。同一区块高度的区块有同样的区块头，区块头是上一个区块高度的哈希值。记账人为同一区块高度并行打包区块。同一区块高度中的区块排序取决于记账人 id 的哈希值。同一区块高度的区块组成一个大区快。
