[toc]

# stackoverflow

## 积分机制

*网址*：https://stackoverflow.com/help/whats-reputation

**增加积分**：

- 提问被赞：+10
- 回答被赞：+10
- 回答被采纳：+15（采纳人 +2）
- 建议的修改被接受：+2（每位用户总计上限为 +1000）

- 回答获得赏金：+赏金全额
- 自动奖励一半赏金给赞最多的回答（悬赏人未手动分配悬赏金）：+一半赏金

- 站点关联奖励：每个站点+100（每个站点最多奖励一次）

每天上限为200积分（赞、踩、建议修改），赏金奖励、采纳回答和关联奖励不包括在限制内。Stack Exchange 网络中超过200积分的用户可以获得100积分的奖励。

**减少积分**：

- 提问被踩：-2
- 回答被踩：-2
- 主动踩回答：-1
- 为问题设置赏金：-赏金全额
- 帖子收到6个垃圾邮件或令人反感的标志：-100

新用户以1积分值开始，并且积分值不会低于1.

## Analysis of the Reputation System and User Contributions on a Question Answering Website: StackOverflow
- 高积分者是回答问题的主力，尤其是高质量的回答
- 大多数的问题是由低积分者提出，高积分者平均提问数量大于低分者
- 通过图分析可以有效检测出用影响力和异常的用户和行为

- 通过提供特权可以激励专家用户持续性贡献

**结论**：

- PageRank与用户专业知识之间没有很好的关联。 但是可以有效地检测到异常用户

- 专家和非专家在现场的参与度均显示出线性增长

- 专家用户一旦加入该网站，便会做出更大的贡献

- 通过用户在站点中一个月的贡献情况，可以可靠地识别专家用户和非专家用户

## Building Reputation in StackOverflow: An Empirical Investigation

**从四个维度分析，给出快速获取积分的方法**

- 积分高体现开发者的知识专业能力
- 积分高获取特权
- 其它用户更信赖积分高的用户

*快速获取积分*：

- 回答与专业知识密度较低的标签相关的问题
- 及时回答问题
- 成为第一个回答问题的人
- 在非高峰时段保持活跃并为不同领域做出贡献

## Towards dynamic interaction-based reputation models

提出 DIB-RM 模型，评估动态积分机制的有效性，该模型主要利用三个因素：forgetting, cumulative, and activity period.