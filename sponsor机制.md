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
  
  - [Wilcoxon 检验之 rank-sum 与 signed-rank](https://blog.csdn.net/chikily_yongfeng/article/details/82255575)
  
  - [两样本Wilcoxon秩和检验法和Mann-Whitney U检验](https://blog.csdn.net/qq_35125180/article/details/107411369)
  - [非参数统计的Python实现—— Wilcoxon 符号秩检验](https://blog.csdn.net/Raider_zreo/article/details/101380293)
  
- cliff's delta：

  - [原论文](http://www.scielo.org.co/scielo.php?script=sci_arttext&pid=S1657-92672011000200018)

  - [计算方法，关键字 cliffs_delta](https://vimsky.com/examples/detail/python-method-scipy.stats.mannwhitneyu.html)

    ![image-20201129115220351](https://i.loli.net/2020/11/29/VIrGfk2Div4qjxw.png)

  - [提到了 cliff's delta 的使用，有一个比例](ELBlocker: Predicting blocking bugs with ensemble imbalance learning)

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

| sponsor times | type  | mean  | std   | min  | 25%   | 50%   | 75%   | max  | count | p                    |
| ------------- | ----- | ----- | ----- | ---- | ----- | ----- | ----- | ---- | ----- | -------------------- |
| 1             | data1 | 322.7 | 264.9 | 0    | 112   | 250   | 469.5 | 1292 | 1135  | 0.08673216884990476  |
| 1             | data2 | 313.9 | 264.3 | 0    | 97.5  | 239   | 480   | 1537 | 1135  |                      |
| 2             | data1 | 342.1 | 268.2 | 0    | 126   | 279   | 499   | 1292 | 737   | 0.3406459005254143   |
| 2             | data2 | 340.0 | 271.7 | 0    | 118   | 282   | 511   | 1537 | 737   |                      |
| 4             | data1 | 340.8 | 260.7 | 2    | 133   | 288   | 510   | 1292 | 365   | 0.6019556669149425   |
| 4             | data2 | 352.5 | 264.4 | 4    | 126   | 309   | 532   | 1361 | 365   |                      |
| 8             | data1 | 345.1 | 286.5 | 2    | 113   | 290   | 511   | 1292 | 177   | 0.36710361290923854  |
| 8             | data2 | 361.0 | 271.5 | 4    | 119   | 320   | 540   | 1298 | 177   |                      |
| 16            | data1 | 389.4 | 301.9 | 7    | 137.8 | 329   | 543   | 1244 | 70    | 0.2989100733280107   |
| 16            | data2 | 411.8 | 263.2 | 22   | 182.5 | 382.5 | 560.5 | 1124 | 70    |                      |
| 32            | data1 | 387.2 | 304.6 | 28   | 118.8 | 356   | 615   | 1188 | 24    | 0.008217697864562005 |
| 32            | data2 | 480.5 | 324.3 | 77   | 181.8 | 476   | 731.3 | 1301 | 24    |                      |
| 64            | data1 | 454.4 | 268.5 | 50   | 329.3 | 376.5 | 658.8 | 870  | 8     | 0.0499499764547464   |
| 64            | data2 | 627   | 138   | 471  | 488.8 | 644   | 744   | 783  | 8     |                      |
| 96            | data1 |       |       |      |       |       |       |      |       |                      |
| 96            | data2 |       |       |      |       |       |       |      |       |                      |
| 128           | data1 |       |       |      |       |       |       |      |       |                      |
| 128           | data2 |       |       |      |       |       |       |      |       |                      |



| percent | type  | mean  | std   | min  | 25%  | 50%  | 75%  | max  | count | p                      |
| ------- | ----- | ----- | ----- | ---- | ---- | ---- | ---- | ---- | ----- | ---------------------- |
| 33%     | data1 | 18.9  | 12.8  | 0    | 8    | 17.5 | 29   | 44   | 650   | 4.383272378767246e-15  |
|         | data2 | 27.8  | 24.8  | 0    | 8    | 21.5 | 39   | 120  |       |                        |
| 67%     | data1 | 83.1  | 24.9  | 45   | 61   | 81   | 105  | 131  | 702   | 0.0012617461031243012  |
|         | data2 | 93.6  | 58.2  | 0    | 48.3 | 82   | 129  | 331  |       |                        |
| 100%    | data1 | 246.7 | 101.3 | 132  | 167  | 216  | 301  | 635  | 655   | 2.6511459046286076e-09 |
|         | data2 | 223.6 | 128.5 | 5    | 125  | 204  | 302  | 701  |       |                        |


## 研究重点和计划

1. 用户分类：不同类型的用户可能对 sponsor 打赏机制的反应不同。

   - 正向分类假设：假设用户的某种特性会影响该用户对 sponsor 打赏机制的反应，从而对用户进行分类。通过分类后的用户群体画出用户活动累计值随时间递增的折线图，验证假设的有效性。影响用户对 sponsor 打赏机制反应的可能特性有：commit 累计值、用户在公司就职或自由职业者
   - 反向分类：画出用户活动累计值随时间递增的折线图，通过判断折线图的类型将用户分为不同类型。判断的方法：人为手动判断、或线性拟合后方差大小判断

   成功分类之后可以查看分类的效果，通过 wilcoxon 做假设性检验

2. 从 sponsorlisting 为起点，开始进行 wilcoxon 分析。时间间隔取 1 周或者 2 周。因为有的用户可能在一到两周发现没有 sponsor 激励，就是淡出持续贡献
3. 实验分析 commit 和 sponsor times 之间的关系是否显著，可通过**关联关系指数（关联分析）**来进行实验
4. 从开发者创建 github 账号开始，从什么时候开始获得 sponsor 打赏对开发者的激励作用越明显。（往往在 github 上开发活动很频繁的用户在获得 sponsor 激励后并不会对开发者的造成实际影响。而对于那些在github上开发活动很少的开发者或新创建的开发者来说，sponsor 激励可能会在短时间内对开发者的开发活动产生很大的正面影响）
5. 使用随机线性回归验证**sponsor 可以激励开发者，使开发者开发活动更加活跃**

