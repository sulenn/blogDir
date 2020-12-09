[toc]

# sponsor 机制

- 打赏开发者或组织：
  - 接收开发者或组织更新动态
  - 在github个人主页显示打赏人的徽章
- sponsor 机制开始时间：May 23, 2019。https://github.blog/2019-05-23-announcing-github-sponsors-a-new-way-to-contribute-to-open-source/

## 研究安排

以某一个接受打赏的用户为起点，爬取打赏的数据、打赏前后开发者活动的数据

### 数据爬取

#### 初步尝试

**数据爬取规则**：先将爬取的数据以 json 文件格式保存，然后再解析 json 文件存储 mysql 数据库中

1. 将用户 *calebporzio* 的用户数据存成 json 文件，然后再存入数据库
2. 爬取 *calebporzio* 的 sponsorlisting 数据，先存 json 文件，再存入数据库
3. 爬取 *calebporzio* 的 tiers 数据，先存 json 文件，再存入数据库
4. 爬取 *calebporzio* 的 sponsorships as maintainer 数据，先存 json 文件，再存入数据库
5. 爬取 *calebporzio* 的 sponsorships as sponsor 数据，先存 json 文件，再存入数据库
6. 爬取 user commit 统计数据（非详细），从 2017-09-01T00:00:00Z 到 2020-11-12T00:00:00Z
7. 爬取 user issue 数据，时间段同上
8. 爬取 user pull request 数据，时间段同上
9. 爬取 user pull request review 数据，时间段同上
10. 爬取 user reposotory 数据（用户创建的 repo），时间段同上
11. 爬取 user commit comment 所有数据
12. 爬取 user issue comment 所有数据

### 其它

修改数据表的字符集：

```shell
alter table TABLE_NAME convert to character set utf8mb4 collate utf8mb4_bin; 
```

### 数据分析方法

- Wilcoxon 秩和检验法
  
  - [wikipedia](https://en.wikipedia.org/wiki/Wilcoxon_signed-rank_test)
  - [Wilcoxon 检验之 rank-sum 与 signed-rank](https://blog.csdn.net/chikily_yongfeng/article/details/82255575)
  - [無母數統計—威爾卡森符號檢定](https://highscope.ch.ntu.edu.tw/wordpress/?p=73978)
  - [Wilcoxon Signed Rank Test](https://www.cnblogs.com/djx571/p/10216940.html)
  - [百度百科-威尔科克森符号秩检验](https://baike.baidu.com/item/%E5%A8%81%E5%B0%94%E7%A7%91%E5%85%8B%E6%A3%AE%E7%AC%A6%E5%8F%B7%E7%A7%A9%E6%A3%80%E9%AA%8C/596168?fr=aladdin)
  - [两样本Wilcoxon秩和检验法和Mann-Whitney U检验](https://blog.csdn.net/qq_35125180/article/details/107411369)
  - [非参数统计的Python实现—— Wilcoxon 符号秩检验](https://blog.csdn.net/Raider_zreo/article/details/101380293)
  
- 双尾和单尾检验：

  - [双尾检测与单尾检测区别](https://blog.csdn.net/zhouwenyuan1015/article/details/103824905)

- cliff's delta：

  - [原论文](http://www.scielo.org.co/scielo.php?script=sci_arttext&pid=S1657-92672011000200018)

  - [计算方法，关键字 cliffs_delta](https://vimsky.com/examples/detail/python-method-scipy.stats.mannwhitneyu.html)

    ![image-20201129115220351](https://i.loli.net/2020/11/29/VIrGfk2Div4qjxw.png)

  - [提到了 cliff's delta 的使用，有一个比例](https://www.cnblogs.com/XBWer/p/4394128.html)

    ![image-20201129115147863](https://i.loli.net/2020/11/29/Z6RBuhUCkOSlLX4.png)

- 获取数据标准差、25%、50%（pandas 中 describe()）

  - [Pandas中describe()函数的使用介绍](https://blog.csdn.net/qq_24754061/article/details/103738513)

- 四分位数（Quartile）：应用于统计学中的箱线图绘制，是统计学中分位数的一种，即把所有数值由小到大排列并分成四等份，处于三个分割点位置的数值就是四分位数。

  **参考**：

  - [四分位数 - 知乎](https://www.zhihu.com/topic/20524765/intro)
  - [Python清除异常值四分位法](https://blog.csdn.net/weixin_43867619/article/details/91838350)

### 图形绘制方法

- 折线图
  - [python 使用matplotlib绘制折线图](https://www.jianshu.com/p/eac606432281)
- 散点图
  - [用python绘制散点图](https://blog.csdn.net/tszupup/article/details/81037411)
  - [python绘制散点图](https://www.jianshu.com/p/6a71afa789a2)
- 箱形图
  - [[seaborn] seaborn学习笔记1-箱形图Boxplot](https://blog.csdn.net/LuohenYJ/article/details/90677918)
  - [seaborn实例-boxplot-箱线图](https://www.jianshu.com/p/869179afe4a1)

## 数据分析结果记录

### commit

**清洗数据**：

- 时间段为三个月

- 记用户被打赏的时间为 t1，如果用户被打赏之前账号创建时间不满 one year，则剔除
- 用户在被 sponsor 前后两个月的 commit 数量需要满足四分位性质
- 清洗四个月活动量为0的数据

**控制变量法**：

- **commit**：33% - 76、67% - 224.25

| percent | type  | mean    | std     | min  | 25%  | 50%  | 75%  | max  | count | p                      | Cliff's Delta    | effective  |
| ------- | ----- | ------- | ------- | ---- | ---- | ---- | ---- | ---- | ----- | ---------------------- | ---------------- | ---------- |
| 33%     | data1 | 33.275  | 22.015  | 0    | 15   | 31   | 51   | 76   | 652   | 1.4306762321969998e-15 | -0.157662595506  | small      |
|         | data2 | 48.247  | 41.163  | 0    | 15   | 38   | 70   | 189  |       |                        |                  |            |
| 67%     | data1 | 139.874 | 41.028  | 77   | 104  | 137  | 172  | 224  | 697   | 0.0012736981198375953  | -0.0250160042321 | negligible |
|         | data2 | 156.750 | 93.864  | 2    | 85   | 138  | 213  | 482  |       |                        |                  |            |
| 100%    | data1 | 415.631 | 173.208 | 225  | 282  | 365  | 502  | 1119 | 647   | 8.131054399308543e-11  | 0.169821957961   | small      |
|         | data2 | 371.781 | 212.602 | 14   | 214  | 329  | 498  | 1194 |       |                        |                  |            |

- **issue**：33% - 0.0、67% - 5.0

| percent | type  | mean   | std   | min  | 25%  | 50%  | 75%  | max  | count | p                      | Cliff's Delta  | effective  |
| ------- | ----- | ------ | ----- | ---- | ---- | ---- | ---- | ---- | ----- | ---------------------- | -------------- | ---------- |
| 33%     | data1 | 0      | 0     | 0    | 0    | 0    | 0    | 0    | 165   | 2.5392755809070115e-31 | -1.0           | large      |
|         | data2 | 1.327  | 0.471 | 1    | 1    | 1    | 2    | 2    |       |                        |                |            |
| 67%     | data1 | 2.309  | 1.372 | 1    | 1    | 2    | 3    | 5    | 726   | 0.5796631510663619     | 0.113516836282 | negligible |
|         | data2 | 2.442  | 2.601 | 0    | 0    | 2    | 4    | 13   |       |                        |                |            |
| 100%    | data1 | 13.519 | 6.648 | 6    | 8    | 10   | 15   | 42   | 486   | 2.6230201978942287e-16 | 0.302452200715 | small      |
|         | data2 | 9.317  | 7.019 | 0    | 4    | 8    | 13   | 36   |       |                        |                |            |

- **pull request**：33% - 1.0、67% - 9.0

| percent | type  | mean   | std    | min  | 25%  | 50%  | 75%  | max  | count | p                      | Cliff's Delta   | effective  |
| ------- | ----- | ------ | ------ | ---- | ---- | ---- | ---- | ---- | ----- | ---------------------- | --------------- | ---------- |
| 33%     | data1 | 0.521  | 0.500  | 0    | 0    | 1    | 1    | 1    | 351   | 2.8435202961650705e-21 | -0.393892906713 | medium     |
|         | data2 | 1.462  | 1.379  | 0    | 0    | 1    | 2    | 5    |       |                        |                 |            |
| 67%     | data1 | 4.589  | 2.146  | 2    | 3    | 4    | 6    | 9    | 616   | 0.42808591920948924    | 0.140292945691  | negligible |
|         | data2 | 4.908  | 4.848  | 0    | 1    | 3    | 7    | 23   |       |                        |                 |            |
| 100%    | data1 | 26.500 | 16.731 | 10   | 14   | 20   | 34   | 107  | 636   | 0.00122777551292113    | 0.115603120921  | negligible |
|         | data2 | 24.576 | 19.116 | 0    | 10   | 20   | 35   | 104  |       |                        |                 |            |

- **pull request review**：33% - 0.0、67% - 4.0

| percent | type  | mean   | std    | min  | 25%  | 50%  | 75%  | max  | count | p                      | Cliff's Delta   | effective  |
| ------- | ----- | ------ | ------ | ---- | ---- | ---- | ---- | ---- | ----- | ---------------------- | --------------- | ---------- |
| 33%     | data1 | 0      | 0      | 0    | 0    | 0    | 0    | 0    | 170   | 1.1685611017032476e-32 | -1.0            | large      |
|         | data2 | 1.282  | 0.452  | 1    | 1    | 1    | 2    | 2    |       |                        |                 |            |
| 67%     | data1 | 1.881  | 1.015  | 1    | 1    | 2    | 3    | 4    | 572   | 0.8092830550468101     | 0.108049293364  | negligible |
|         | data2 | 2.117  | 2.338  | 0    | 0    | 1    | 3    | 10   |       |                        |                 |            |
| 100%    | data1 | 15.339 | 11.340 | 5    | 7    | 11   | 21   | 60   | 560   | 0.20050009508067257    | 0.0628954081633 | negligible |
|         | data2 | 15.214 | 12.953 | 0    | 5    | 11   | 22   | 70   |       |                        |                 |            |

- **repository**：33% - 0.0、67% - 2.0

| percent | type  | mean  | std   | min  | 25%  | 50%  | 75%  | max  | count | p                      | Cliff's Delta  | effective  |
| ------- | ----- | ----- | ----- | ---- | ---- | ---- | ---- | ---- | ----- | ---------------------- | -------------- | ---------- |
| 33%     | data1 | 0     | 0     | 0    | 0    | 0    | 0    | 0    | 227   | 2.1767072104316712e-42 | -1.0           | large      |
|         | data2 | 0.330 | 0.471 | 1    | 1    | 1    | 2    | 2    |       |                        |                |            |
| 67%     | data1 | 1.354 | 0.479 | 1    | 1    | 1    | 2    | 2    | 734   | 0.7393182564043641     | 0.139046247281 | negligible |
|         | data2 | 1.394 | 1.484 | 0    | 0    | 1    | 2    | 6    |       |                        |                |            |
| 100%    | data1 | 5.262 | 2.519 | 3    | 3    | 4    | 6    | 19   | 557   | 1.2601199669973845e-23 | 0.362057573111 | medium     |
|         | data2 | 3.749 | 3.103 | 0    | 1    | 3    | 5    | 14   |       |                        |                |            |

- **commit comment**：33% - 0.0、67% - 2.0

| percent | type  | mean  | std   | min  | 25%  | 50%  | 75%  | max  | count | p                      | Cliff's Delta | effective  |
| ------- | ----- | ----- | ----- | ---- | ---- | ---- | ---- | ---- | ----- | ---------------------- | ------------- | ---------- |
| 33%     | data1 | 0     | 0     | 0    | 0    | 0    | 0    | 0    | 227   | 2.1767072104316712e-42 | -1.0          | large      |
|         | data2 | 0.330 | 0.471 | 1    | 1    | 1    | 2    | 2    |       |                        |               |            |
| 67%     | data1 |       |       |      |      |      |      |      |       |                        |               | negligible |
|         | data2 |       |       |      |      |      |      |      |       |                        |               |            |
| 100%    | data1 |       |       |      |      |      |      |      |       |                        |               | medium     |
|         | data2 |       |       |      |      |      |      |      |       |                        |               |            |

- **issue comment**：33% - 13、67% - 56

| percent | type  | mean    | std    | min  | 25%  | 50%  | 75%    | max  | count | p                      | Cliff's Delta    | effective  |
| ------- | ----- | ------- | ------ | ---- | ---- | ---- | ------ | ---- | ----- | ---------------------- | ---------------- | ---------- |
| 33%     | data1 | 5.049   | 4.032  | 0    | 1    | 4    | 8      | 13   | 552   | 1.1587297615338252e-21 | -0.215835696282  | small      |
|         | data2 | 9.359   | 9.308  | 0    | 2    | 6    | 15     | 45   |       |                        |                  |            |
| 67%     | data1 | 31.215  | 12.247 | 14   | 21   | 30   | 41     | 56   | 684   | 0.004482231799886918   | -0.0191939400157 | negligible |
|         | data2 | 36.522  | 26.504 | 0    | 15   | 30   | 50     | 122  |       |                        |                  |            |
| 100%    | data1 | 117.186 | 52.795 | 57   | 76   | 100  | 147.75 | 312  | 598   | 1.116249929904819e-06  | 0.137325085849   | medium     |
|         | data2 | 106.595 | 63.527 | 0    | 58   | 96   | 141    | 364  |       |                        |                  |            |

## 研究重点和计划

1. 用户分类：不同类型的用户可能对 sponsor 打赏机制的反应不同。

   - 正向分类假设：假设用户的某种特性会影响该用户对 sponsor 打赏机制的反应，从而对用户进行分类。通过分类后的用户群体画出用户活动累计值随时间递增的折线图，验证假设的有效性。影响用户对 sponsor 打赏机制反应的可能特性有：commit 累计值、用户在公司就职或自由职业者
   - 反向分类：画出用户活动累计值随时间递增的折线图，通过判断折线图的类型将用户分为不同类型。判断的方法：人为手动判断、或线性拟合后方差大小判断

   成功分类之后可以查看分类的效果，通过 wilcoxon 做假设性检验

2. 从 sponsorlisting 为起点，开始进行 wilcoxon 分析。时间间隔取 1 周或者 2 周。因为有的用户可能在一到两周发现没有 sponsor 激励，就是淡出持续贡献
3. 实验分析 commit 和 sponsor times 之间的关系是否显著，可通过**关联关系指数（关联分析）**来进行实验
4. 从开发者创建 github 账号开始，从什么时候开始获得 sponsor 打赏对开发者的激励作用越明显。（往往在 github 上开发活动很频繁的用户在获得 sponsor 激励后并不会对开发者的造成实际影响。而对于那些在github上开发活动很少的开发者或新创建的开发者来说，sponsor 激励可能会在短时间内对开发者的开发活动产生很大的正面影响）
5. 使用随机线性回归验证**sponsor 可以激励开发者，使开发者开发活动更加活跃**

