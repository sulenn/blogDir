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

**数据分析说明和结果**：取用户获得的第一笔赞助为开始时间，记为 t1。t2 是 2020-11-12T00:00:00Z。通过 2*t1-t2 求得 t0。对比赞助之前（t0 - t1）和赞助之后（t1-t2）用户贡献数据的数量差异。

- *user commit*：sum1（before sponsoring）=1282，sum2（after sponsoring）=2401

  ![image-20201113203226229](https://i.loli.net/2020/11/13/L2MSClmKrhq5GIb.png)

- *user issue*：sum1=6，sum2=10

  ![image-20201113203319526](https://i.loli.net/2020/11/13/VwShvrjsI7gkTOe.png)

- *user pull request*：sum1=57，sum2=93

- *user pull request review*：sum1=11，sum2=8
- *user repository*：sum1=15，sum2=20
- *user commit comment*：sum1=7，sum2=0
- *user issue comment*：sum1=684，sum2=1515



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

- 时间段为两个月

- 记用户被打赏的时间为 t1，如果用户被打赏之前账号创建时间不满 one year，则剔除
- 用户在被 sponsor 前后两个月的 commit 数量需要满足四分位性质
- 清洗四个月活动量为0的数据

**控制变量法**：

| sponsor times | type  | mean  | std   | min  | 25%   | 50%   | 75%   | max  | count | p                      |
| ------------- | ----- | ----- | ----- | ---- | ----- | ----- | ----- | ---- | ----- | ---------------------- |
| 1             | data1 | 216.7 | 181.8 | 0    | 73    | 167   | 314   | 894  | 1219  | 4.237995769195141e-200 |
| 1             | data2 | 208.1 | 176.4 | 0    | 66    | 160   | 320   | 925  | 1219  |                        |
| 2             | data1 | 229.3 | 188.6 | 0    | 81    | 176   | 329   | 894  | 963   | 7.613051096983189e-159 |
| 2             | data2 | 222.4 | 182.1 | 0    | 77    | 178   | 339   | 950  | 963   |                        |
| 4             | data1 | 239.0 | 187.2 | 0    | 91    | 196   | 345   | 894  | 633   | 3.576412799410171e-105 |
| 4             | data2 | 236.9 | 186.7 | 0    | 83    | 192   | 351   | 950  | 633   |                        |
| 8             | data1 | 249.3 | 191.4 | 0    | 94    | 216   | 361   | 894  | 368   | 4.628293065987817e-62  |
| 8             | data2 | 251.6 | 189.9 | 1    | 95.5  | 223   | 375   | 950  | 368   |                        |
| 16            | data1 | 252.6 | 200.1 | 0    | 91    | 223   | 363.5 | 894  | 187   | 1.9434149013601442e-32 |
| 16            | data2 | 261.3 | 190.5 | 1    | 97.5  | 230   | 380   | 950  | 187   |                        |
| 32            | data1 | 280.6 | 225.1 | 6    | 99.25 | 226.5 | 419.5 | 1051 | 90    | 1.7436685425802348e-16 |
| 32            | data2 | 311.7 | 221.9 | 3    | 131.5 | 273   | 424.5 | 1012 | 90    |                        |
| 64            | data1 | 269.6 | 194.3 | 38   | 96    | 237   | 407   | 736  | 37    | 1.140214479510659e-07  |
| 64            | data2 | 305.9 | 175.2 | 45   | 174   | 279   | 381   | 709  | 37    |                        |
| 96            | data1 | 265.7 | 186.2 | 38   | 171   | 234   | 415   | 736  | 17    | 0.0002930525201924893  |
| 96            | data2 | 307.2 | 160.3 | 53   | 174   | 307   | 387   | 554  | 17    |                        |
| 128           | data1 | 357.2 | 206.9 | 49   | 210   | 330   | 455   | 736  | 12    | 0.002217721464237049   |
| 128           | data2 | 401.6 | 146.4 | 172  | 345   | 384   | 527.5 | 623  | 12    |                        |

#### sponsor 后和 sponsor 前 commit 数量的比值

- one month, sponsor times range:1 - 128

  ![image-20201128212813617](https://i.loli.net/2020/11/28/n7sNkdm4g59qY3c.png)

- two month，sponsor times range: 1 - 128

  ![image-20201128211830315](https://i.loli.net/2020/11/28/lxNFoQAghaMGpCW.png)

- three month, sponsor times range:1 - 128

  ![image-20201128212418767](https://i.loli.net/2020/11/28/H2pFAdD9fMRbxCe.png)

  ![image-20201128220834423](https://i.loli.net/2020/11/28/eL4UB6M1f38DxjO.png)

- four month, sponsor times range:1 - 128

  ![image-20201128212533043](https://i.loli.net/2020/11/28/2oerVg8HMOFPA46.png)

  ![image-20201128221058660](https://i.loli.net/2020/11/28/mCaRZpjfNkelgJt.png)

- five month, sponsor times range: 1 - 128

  ![image-20201128213114715](https://i.loli.net/2020/11/28/18rCXSD9tTpUYwx.png)

- six month, sponsor times range:1 - 128

  ![image-20201128213532236](https://i.loli.net/2020/11/28/BngWCRajJIhsVTO.png)

- three month，sponsor times range: 1 - 128, 2

### issue comment

**清洗数据**：

- 记用户被打赏的时间为 t1，如果用户被打赏之前账号创建时间不满 one year，则剔除
- 用户在被 sponsor 前后两个月的 commit 数量需要满足四分位性质
- 清除用户在被 sponsor  前后 issue comment 数量为 0 的情况

**two month, one_year**：

| sponsor times | mean | std  | min  | 25%  | 50%  | 75%  | max  |
| ------------- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 1             |      |      |      |      |      |      |      |

### 

