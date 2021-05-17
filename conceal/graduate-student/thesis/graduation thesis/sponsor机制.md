[toc]

# sponsor 机制

- 打赏开发者或组织：
  - 接收开发者或组织更新动态
  - 在github个人主页显示打赏人的徽章
  
- sponsor 机制开始时间：May 23, 2019。https://github.blog/2019-05-23-announcing-github-sponsors-a-new-way-to-contribute-to-open-source/

- start time of Sponsors Matching Fund：2019年6月。https://github.community/t/github-sponsors-faq/10217

  ![image-20201211212202970](https://i.loli.net/2020/12/11/OWx2MeHFRVKkYr4.png)

- **打赏可以获得的好处**：

  - 被打赏人：

    - 金钱激励
    - 一/多杯果汁、一/多杯咖啡、一/多杯啤酒、wifi
    - 开源项目持续集成费用、云服务器费用、到处飞开会和宣传费用、云存储费用、个人电脑费用、付费软件费用、域名费用、分布式环境测试费用
    - 写博客、出教学视频
    - 按照不同打赏金额，在24小时内或12小时内响应打赏人请求

  - 打赏人：

    - 被打赏人Github个人主页或其它博客获得一个打赏图标（名字、图片）或链接（打赏人主页），可以是个人图标或公司图标

      ![image-20201212101053566](https://i.loli.net/2020/12/12/x2b4snMERGrmayF.png)

    - 打赏人获得Sponsor徽章在Github个人主页

      ![image-20201212102059688](https://i.loli.net/2020/12/12/HGJFvlKyAgwVk62.png)

    - 被打赏人帮助打赏人审阅pr，提供技术指导、架构设计

      ![image-20201212100946371](https://i.loli.net/2020/12/12/NgEZiPK4mz5MYjU.png)

    - 被打赏人帮助打赏人维护一个个人或公司项目

      ![image-20201212101009025](https://i.loli.net/2020/12/12/2vGWmJsZ579lrNp.png)

    - 获得被打赏人的电话，有事电话、发短信

      ![image-20201212101643780](https://i.loli.net/2020/12/12/d6NUYVF2hEucOl7.png)

    - 打赏人可以收到被打赏人每周的项目进展状态邮件

      ![image-20201212101809284](https://i.loli.net/2020/12/12/xplU4bOTLGQZf2k.png)

    - 被打赏人维护一个Sponsors列表，不同的打赏金额对应不同的级别。青铜赞助商、白银赞助商、黄金赞助商

      ![image-20201212110555397](https://i.loli.net/2020/12/12/b4INuMgJYV5QPyf.png)

    - 打赏人可以参与项目私人讨论组（用户：mfix22，于2019.09.23创建）

      ![image-20201212161811993](https://i.loli.net/2020/12/12/uhMtmsnFZcOfwWA.png)

    - 被打赏人优先处理打赏人pr：![image-20201214144705856](https://i.loli.net/2020/12/14/dgOzGPbJ4MvYs3I.png)

- **sponsors goals**：包括两种goals类型，第一种是打赏人数要求，第二种是打赏总金额数要求

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
  
- 趋势线：趋势线是数据趋势的图形化表现形式，可用于数据的分析与预测。这种分析又被称为回归分析，通过回归分析，可以将图表中的趋势线延伸至事实数据之外，预测未来值，分析过去值。

  - [图表趋势线原理](https://help.fanruan.com/finereport/doc-view-2876.html)

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

- **issue**：50% - 2.0，只用50%是因为33%会出现0，导致Cliff's Delta为-1。因为用四分位清洗数据时，把大部分数据都清洗掉了，比如小于50%的用户从1185清洗到了613，所以去掉四分位清洗。

| percent | type  | mean   | std    | min  | 25%  | 50%  | 75%  | max  | count-before-clear | count | p                     | Cliff's Delta   | effective |
| ------- | ----- | ------ | ------ | ---- | ---- | ---- | ---- | ---- | ------------------ | ----- | --------------------- | --------------- | --------- |
| 50%     | data1 | 0.542  | 0.933  | 0    | 0    | 0    | 1    | 2    | 1185               | 1185  | 7.868293289333099e-49 | -0.186649575389 | small     |
|         | data2 | 2.125  | 5.860  | 0    | 0    | 0    | 2    | 137  |                    |       |                       |                 |           |
| 100%    | data1 | 13.482 | 15.635 | 3    | 5    | 8    | 15   | 139  | 922                | 922   | 2.184890638448491e-08 | 0.155404642365  | small     |
|         | data2 | 12.345 | 16.932 | 0    | 3    | 7    | 14   | 181  |                    |       |                       |                 |           |

- **pull request**：33% - 1.0、67% - 9.0

| percent | type  | mean   | std    | min  | 25%  | 50%  | 75%  | max  | count | p                      | Cliff's Delta   | effective  |
| ------- | ----- | ------ | ------ | ---- | ---- | ---- | ---- | ---- | ----- | ---------------------- | --------------- | ---------- |
| 33%     | data1 | 0.521  | 0.500  | 0    | 0    | 1    | 1    | 1    | 351   | 2.8435202961650705e-21 | -0.393892906713 | medium     |
|         | data2 | 1.462  | 1.379  | 0    | 0    | 1    | 2    | 5    |       |                        |                 |            |
| 67%     | data1 | 4.589  | 2.146  | 2    | 3    | 4    | 6    | 9    | 616   | 0.42808591920948924    | 0.140292945691  | negligible |
|         | data2 | 4.908  | 4.848  | 0    | 1    | 3    | 7    | 23   |       |                        |                 |            |
| 100%    | data1 | 26.500 | 16.731 | 10   | 14   | 20   | 34   | 107  | 636   | 0.00122777551292113    | 0.115603120921  | negligible |
|         | data2 | 24.576 | 19.116 | 0    | 10   | 20   | 35   | 104  |       |                        |                 |            |

- **pull request comment**：

  - *don't include commit comment*：33% - 2.0、67% - 15.0

    | percent | type  | mean   | std    | min  | 25%  | 50%  | 75%  | max  | count | p                      | Cliff's Delta   | effective  |
    | ------- | ----- | ------ | ------ | ---- | ---- | ---- | ---- | ---- | ----- | ---------------------- | --------------- | ---------- |
    | 33%     | data1 | 0.816  | 0.779  | 0    | 0    | 1    | 1    | 2    | 391   | 5.270378481007365e-30  | -0.44489504909  | medium     |
    |         | data2 | 2.593  | 2.465  | 0    | 1    | 2    | 4    | 10   |       |                        |                 |            |
    | 67%     | data1 | 7.679  | 3.663  | 3    | 4    | 7    | 10   | 15   | 629   | 0.5338583045598942     | 0.0948334474941 | negligible |
    |         | data2 | 8.130  | 7.346  | 0    | 2    | 6    | 12   | 36   |       |                        |                 |            |
    | 100%    | data1 | 39.144 | 22.801 | 16   | 22   | 32   | 49   | 142  | 591   | 1.3149254757618952e-05 | 0.151121876083  | small      |
    |         | data2 | 35.470 | 27.373 | 0    | 14   | 29   | 51   | 136  |       |                        |                 |            |

  - *include commit comment and pull request review*：33% - 3.0、67% - 22.0

    | percent | type  | mean   | std    | min  | 25%  | 50%  | 75%  | max  | count | p                      | Cliff's Delta   | effective  |
    | ------- | ----- | ------ | ------ | ---- | ---- | ---- | ---- | ---- | ----- | ---------------------- | --------------- | ---------- |
    | 33%     | data1 | 1.126  | 1.035  | 0    | 0    | 1    | 2    | 3    | 430   | 3.3499825553147495e-21 | -0.32133585722  | small      |
    |         | data2 | 2.593  | 2.589  | 0    | 1    | 2    | 4    | 11   |       |                        |                 |            |
    | 67%     | data1 | 10.562 | 5.079  | 4    | 6    | 10   | 14   | 22   | 675   | 0.7170513266026239     | 0.0471330589849 | negligible |
    |         | data2 | 11.554 | 9.672  | 0    | 4    | 9    | 17   | 46   |       |                        |                 |            |
    | 100%    | data1 | 56.113 | 31.829 | 23   | 31   | 47   | 73   | 586  | 586   | 0.007553570115314566   | 0.0937605563256 | small      |
    |         | data2 | 53.386 | 37.267 | 0    | 25   | 44   | 75   | 181  |       |                        |                 |            |

- **pull request review**：50% - 1，因为用四分位清洗数据时，把大部分数据都清洗掉了，比如小于50%的用户从1098清洗到了412，所以去掉四分位清洗。只用50%是因为33%会出现0，导致Cliff's Delta为-1

| percent | type  | mean   | std    | min  | 25%  | 50%  | 75%  | max  | count-before-clear | count | p                     | Cliff's Delta   | effective  |
| ------- | ----- | ------ | ------ | ---- | ---- | ---- | ---- | ---- | ------------------ | ----- | --------------------- | --------------- | ---------- |
| 50%     | data1 | 0.254  | 0.436  | 0    | 0    | 0    | 1    | 1    | 1098               | 1098  | 4.698538407282026e-41 | -0.169772163994 | small      |
|         | data2 | 1.186  | 3.428  | 0    | 0    | 0    | 1    | 57   |                    |       |                       |                 |            |
| 100%    | data1 | 19.5   | 34.387 | 2    | 3    | 7    | 21   | 524  | 1009               | 1009  | 0.7876526779478418    | 0.0353468928307 | negligible |
|         | data2 | 20.356 | 38.505 | 0    | 3    | 9    | 23   | 653  |                    |       |                       |                 |            |

- **repository**：50% - 1，因为用四分位清洗数据时，把大部分数据都清洗掉了，比如小于50%的用户从1221清洗到了227，所以去掉四分位清洗。只用50%是因为33%会出现0，导致Cliff's Delta为-1

| percent | type  | mean  | std   | min  | 25%  | 50%  | 75%  | max  | count-before-clear | count | p                     | Cliff's Delta   | effective |
| ------- | ----- | ----- | ----- | ---- | ---- | ---- | ---- | ---- | ------------------ | ----- | --------------------- | --------------- | --------- |
| 50%     | data1 | 0.404 | 0.491 | 0    | 0    | 0    | 1    | 1    | 1221               | 1221  | 6.76481930138173e-54  | -0.220383662644 | small     |
|         | data2 | 1.229 | 1.905 | 0    | 0    | 1    | 2    | 16   |                    |       |                       |                 |           |
| 100%    | data1 | 4.625 | 3.786 | 2    | 2    | 3    | 6    | 49   | 886                | 886   | 6.645832583396771e-15 | 0.233692910537  | small     |
|         | data2 | 3.839 | 4.258 | 0    | 1    | 3    | 5    | 37   |                    |       |                       |                 |           |

- **commit comment**：33% - 0.0、67% - 2.0

  

- **issue comment**：33% - 7、67% - 36

  | percent | type  | mean   | std    | min  | 25%  | 50%  | 75%   | max  | count | p                      | Cliff's Delta   | effective  |
  | ------- | ----- | ------ | ------ | ---- | ---- | ---- | ----- | ---- | ----- | ---------------------- | --------------- | ---------- |
  | 33%     | data1 | 2.645  | 2.282  | 0    | 1    | 2    | 4     | 7    | 462   | 2.0142359265382374e-14 | -0.228036393621 | small      |
  |         | data2 | 4.920  | 4.948  | 0    | 1    | 3    | 8     | 21   |       |                        |                 |            |
  | 67%     | data1 | 18.648 | 8.146  | 8    | 12   | 17   | 25    | 36   | 650   | 0.282562067145546      | 0.0249159763314 | negligible |
  |         | data2 | 20.506 | 15.673 | 0    | 8    | 17   | 29    | 73   |       |                        |                 |            |
  | 100%    | data1 | 78.637 | 39.634 | 37   | 49   | 66   | 98    | 320  | 622   | 3.6678539098956137e-06 | 0.144803093434  | negligible |
  |         | data2 | 71.355 | 46.652 | 0    | 36   | 62   | 98.75 |      |       |                        |                 |            |

## 研究重点和计划

1. 用户分类：不同类型的用户可能对 sponsor 打赏机制的反应不同。

   - 正向分类假设：假设用户的某种特性会影响该用户对 sponsor 打赏机制的反应，从而对用户进行分类。通过分类后的用户群体画出用户活动累计值随时间递增的折线图，验证假设的有效性。影响用户对 sponsor 打赏机制反应的可能特性有：commit 累计值、用户在公司就职或自由职业者
   - 反向分类：画出用户活动累计值随时间递增的折线图，通过判断折线图的类型将用户分为不同类型。判断的方法：人为手动判断、或线性拟合后方差大小判断

   成功分类之后可以查看分类的效果，通过 wilcoxon 做假设性检验

3. 实验分析 commit 和 sponsor times 之间的关系是否显著，可通过**关联关系指数（关联分析）**来进行实验
4. 从开发者创建 github 账号开始，从什么时候开始获得 sponsor 打赏对开发者的激励作用越明显。（往往在 github 上开发活动很频繁的用户在获得 sponsor 激励后并不会对开发者的造成实际影响。而对于那些在github上开发活动很少的开发者或新创建的开发者来说，sponsor 激励可能会在短时间内对开发者的开发活动产生很大的正面影响）
5. 使用随机线性回归验证**sponsor 可以激励开发者，使开发者开发活动更加活跃**

## 活动上升、下降的原因分析

- xabbuh：

  ![image-20201223101645305](https://i.loli.net/2020/12/23/MZUpo3IE7vugVLP.png)-

- vertexclique：

  ![image-20201223102601698](https://i.loli.net/2020/12/23/gnMX4adpjyJCOos.png)

- terrytangyuan：

  ![image-20201223105548451](https://i.loli.net/2020/12/23/COGQgT5IFi6XwSs.png)

- stancl：

  ![image-20201223120404848](https://i.loli.net/2020/12/23/AFnj17HhEOJdsva.png)

- shiftkey：

  ![image-20201223120646491](https://i.loli.net/2020/12/23/3cjQC9agf8Rd2VW.png)

- sharkdp：

  ![image-20201223120806007](https://i.loli.net/2020/12/23/91XKjabVkWZ2S6q.png)

- reinink：

  ![image-20201223121022740](https://i.loli.net/2020/12/23/pSqaEOlL3KjyhAV.png)

- phil-opp：https://os.phil-opp.com/ 个人博客

  ![image-20201223121143778](https://i.loli.net/2020/12/23/YU39Re6f7hsTIpX.png)

- ondrejmirtes：

  ![image-20201221202404205](https://i.loli.net/2020/12/21/q9GtYNU4aibAuFJ.png)

- olilarkin：

  ![image-20201221203111672](https://i.loli.net/2020/12/21/YcbArMwUgaoE3OL.png)

- msurguy：

  ![image-20201221205303773](https://i.loli.net/2020/12/21/N7lmpsFh1Aaotcx.png)

- mstksg：

  ![image-20201221205946863](https://i.loli.net/2020/12/21/ZLEAiCbRh87wNs1.png)

- MikeMcQuaid：

  ![image-20201221210951469](https://i.loli.net/2020/12/21/FCRcw3BgqfVvHLk.png)

- akyoto：

  ![image-20201222105109829](https://i.loli.net/2020/12/22/8mLNTejiZ41ChvG.png)

- antelle：

  ![image-20201222142240153](https://i.loli.net/2020/12/22/iyOpSvzjIsN9hm6.png)

- bbatsov：

  ![image-20201222143214091](https://i.loli.net/2020/12/22/jEnuIUJBxraZ9Rs.png)

  ![image-20201222144023473](https://i.loli.net/2020/12/22/jh2gWSbNAxz1VF7.png)

- cart：

  ![image-20201222145028223](https://i.loli.net/2020/12/22/w2CQPxVueKcm4WY.png)

- ioquatix：

  ![image-20201222152501725](https://i.loli.net/2020/12/22/tdVsOqRkL59erpy.png)

- hueniverse：

  ![image-20201222153016338](https://i.loli.net/2020/12/22/M7TpIGS1lhyL9Uv.png)

- foosel：

  ![image-20201222153640061](https://i.loli.net/2020/12/22/KEBafW87NCb9p4l.png)

### 100%类型用户特征分析

- mfix22，打赏之前984活动量，打赏之后308活动量，sponsorlisting 创建时间为2019-09-21 05:18:43，sponsored 创建时间为2019-11-13 06:46:31，1个打赏人

  ![image-20201221124512356](https://i.loli.net/2020/12/21/ONDXCvTV5R3rpWl.png)

- walaura：打赏之前368活动量，打赏之后32活动量，sponsorlisting 创建时间为2019-10-24 17:28:05，sponsored 创建时间为2019-10-25 15:06:47，11个打赏人

  ![image-20201221124152850](https://i.loli.net/2020/12/21/ZeGJxWQtamLTszE.png)

- tpope：*这个人的活动曲线非常有意思*

  ![image-20201223152521555](https://i.loli.net/2020/12/23/ApWu1lBaI2YvTQP.png)

- revertdata：

  ![image-20201221163837481](https://i.loli.net/2020/12/21/JvnBOWlmLzTuce3.png)

- kjk：

  ![image-20201221214220509](https://i.loli.net/2020/12/21/UvT5eJV28G7crxh.png)

- jxnblk：

  ![image-20201221214315790](https://i.loli.net/2020/12/21/zIsFQ4TvxLfjimZ.png)

- indutny：

  ![image-20201221215925888](https://i.loli.net/2020/12/21/O1RtxMWPoJK5Zrz.png)

- desimaniac：

  ![image-20201221222607717](https://i.loli.net/2020/12/21/9rGUFNVXalSB1ev.png)

**导致100%类型开发者开发活动下降的可能原因**：

- Commit开发活动降低，issue comment开发活动升高（开源项目趋向成熟，开发者只需要维护，但是修复bug比开发新功能更耗时，所以commit贡献数量下降）
- 未到达设定的 Sponsors Goal 
- 主要参与javascript类型开源项目（不一定，可能和数据爬取时使用的策略有关，前端开发工程师互相打赏、扎堆爬取。验证：爬取所有开发者参与的最活跃的开源项目所使用的语言）

### 33%类型用户特征分析

- sponsors profile 中表明接受 sponsor 会花费更多时间在开源贡献上
  - ### sindresorhus

  ![image-20201217143618891](https://i.loli.net/2020/12/17/V8tukb2FNDnhz63.png)

  - ljharb：

  ![image-20201217144239444](https://i.loli.net/2020/12/17/z8usdmKEFWb9NYG.png)

- funkypenguin（打赏人可以在出版的书中的标示）：https://github.com/sponsors/funkypenguin

  ![image-20201219153519521](https://i.loli.net/2020/12/19/LukrhEo6HftnzcP.png)

  ![image-20201220084746217](https://i.loli.net/2020/12/20/eCoIgcUHPuG6DKw.png)

- vktr：

  ![image-20201220120653123](https://i.loli.net/2020/12/20/3KVG19PXDImtSgu.png)

- vis2k：效果明显，Commit活动激增，被 sponsors 的次数也非常多

  ![image-20201220120605430](https://i.loli.net/2020/12/20/Hv8l32MtujrkDNL.png)

- szmarczak：commit、issue comment、pr mixture上升都很明显

  ![image-20201220120502002](https://i.loli.net/2020/12/20/FqZEWeDiUA9o1gu.png)

- **ramsey**：又一个 commit 激励效果非常明显的案例，且受到 sponsors 次数较多

  ![image-20201220115848052](https://i.loli.net/2020/12/20/cPQeEUsV2xvOTki.png)

- **lastzero**：很明显的 commit 和 issue comment 激励，且受到的 sponsors 次数较多

  ![image-20201220115723324](https://i.loli.net/2020/12/20/OvPezbBLwNpA4yQ.png)

**Sponsors激励机制下开发活动呈显著上升的开发者特征**：

- 开通Sponsors激励机制之前一段时间内开发者开发活动较少

- 开发者均参与贡献最少一个Star数很高、很活跃的开源项目

- 开发者主要开发活动为Commit，极少数用户的Issue Comment开发活动比Commit高

- 开发者在Sponsors Profile或Sponsors tiers中表示打赏会激励开发者为开源社区做更多的贡献，比如花更多的时间、优先回答打赏人的问题、及时处理feature和bug等

  ![image-20201221123503144](https://i.loli.net/2020/12/21/Zy5PuN1W9jGp3iX.png)

![image-20201221120758411](https://i.loli.net/2020/12/21/UoeirJgWOBhIFCd.png)

![image-20201221120834599](https://i.loli.net/2020/12/21/djPH1T5ekSKmch6.png)

![image-20201221123700460](https://i.loli.net/2020/12/21/iyE4Djuo3pmlY5H.png)

![image-20201221123804628](https://i.loli.net/2020/12/21/eVFr5YcXQvSMxAd.png)