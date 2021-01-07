# PoPF: A Consensus Algorithm for JCLedger

## Abstract

共识算法是 JCLedger 中最重要的元素。原始的 POW 算法不适用 JCLedger，因为参与者算力不平等，存在记账垄断的风险，同时吞吐量不满足大规模要求。

Proof of Participation and Fees（PoPF），基于 PoW 共识算法，可以存储算力并且更有效，针对 JCLedger。设计中，只有候选者有挖矿的权力，候选人选择由两个因素决定：成为记账人的次数，以及参与者支付的费用。候选人解决哈希难题的困难度不同（排名越高越轻松）。

模拟实验表示记账人分布非常平衡，参与者中算力不平衡问题得到解决，参与者有相同的机会成为记账人。
