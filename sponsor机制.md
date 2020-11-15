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