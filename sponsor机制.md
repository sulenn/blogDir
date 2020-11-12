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







```json
query {
    user(login:"dlemstra") {
        name
        email
        login
        sponsorsListing {
            createdAt
            fullDescription
            name
            shortDescription
            slug
            tiers(first:10) {
                totalCount
                edges {
                    node {
                        createdAt
                        description
                        monthlyPriceInCents
                        monthlyPriceInDollars
                        name
                        updatedAt
                    }
                }
            }
        }
        sponsorshipsAsMaintainer {
            totalCount
        }
        sponsorshipsAsSponsor {
            totalCount
        }
    }
}
```



