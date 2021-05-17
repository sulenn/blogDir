[toc]

# nodejs 命令行工具部署与接口说明文档

## 命令行工具部署

**环境要求**：

1. nodejs：10.20.1 以上

2. npm：6.14.4 以上

**安装**：

1. `git clone -b trustie http://git.trustie.net/qiubing/chain_creator_nodejs_trustie_fabric2.git`

2. `npm install -g`

**部署安装完成**。

---

## 命令行接口

**命令行使用方式为**：chain + 命令类型 + 参数

**当前可用命令行接口数量为**：8

### trustieCreate

trustie 创建平台创建项目完成之后，调用该方法创建远程项目链。==该方法创建时间大概为20 - 30s==

**命令参数**：`chain trustieCreate ownername reponame`

其中 ownername 为创建项目的人；reponame 为项目名。

**输出**：json 格式输出 - {status:int, message:string}；status 200 为成功，404 则为失败

**举例**：

- 命令：`chain trustieCreate sulenn trustie1`

- 输出：`{ status: 200, message: '远程项目链初始化创建成功！' }`

### trustiePush

trustie 接受到开发者提交的 push 命令（或者从 gitea 返回的 push 成功之类的回调）之后，调用 trustiePush 将关键数据上链

**命令参数**：`chain trustiePush ownername reponame username uploadPushInfo`

其中 ownername 为创建项目的人；reponame 为项目名；username 为提交 push 的人；uploadPushInfo 为待记录的关键数据

uploadPushInfo 需要满足一定格式要求：

```json
{
    "shas": ["f1b965795dbfb73af9c446d5c158254a955b1d28", "46f68a7252ba0af072e634295b61f39407ceea01"],
    "branch": "master",
    "ownername": "sulennnnn",
    "reponame": "sullenn3",
    "username": "sulennnnn",
    "modificationLines":5
}
```

shas：为当前 push 中包含的所有 commit hash 值
branch：为当前 push 所属分支
ownername：为创建该项目的人
reponame：项目名
username：提交该次 push 的人
modificationLines：为当前 push 中包含的所有变更代码行数（增、删代码行数）

**输出**：json 格式输出 - {status:int, message:string}；status 200 为成功，404 则为失败

**举例**：

- 命令：`chain trustiePush sulenn qiubing15 sulenn "{\"shas\":[\"f1b965795dbfb73af9c446d5c158254a955b1d28\",\"46f68a7252ba0af072e634295b61f39407ceea01\"],\"branch\":\"master\",\"ownername\":\"sulennnnn\",\"reponame\":\"sullenn3\",\"username\":\"sulennnnn\",\"modificationLines\":5}"`

- 输出：`{ status: 200, message: 'TrustiePush 调用成功' }`

### trustiePullRequest

trustie 某个项目核心开发者合并外围开发者提交的 pull request 请求之后，调用 trustiePullRequest 将关键数据上链记录

**命令参数**：`chain trustiePullRequest ownername reponame uploadPushInfo`

其中 ownername 为创建项目的人；reponame 为项目名；uploadPushInfo 为待记录的关键数据

uploadPushInfo 需要满足一定格式要求：

```json
{
    "source_branch": "Master",
    "target_branch": "Master",
    "source_project_id": 23,
    "target_project_id": 24,
    "ownername":"sulenn",
    "username": "sulennnn",
    "reponame": "sullenn3",
    "shas": ["f1b965795dbfb73af9c446d5c158254a955b1d28","46f68a7252ba0af072e634295b61f39407ceea01"],
    "modificationLines": 5
}
```

source_branch：为发起 pull request 请求的 fork 项目分支，比如 A1 是从 A 项目 fork 得到的，那么 A1 所属分支就是 source_branch
target_branch：比如 A1 是从 A 项目 fork 得到的，那么 A 所属分支就是 target_branch
source_project_id：A1 项目所属编号
target_project_id：A 项目所属编号
ownername：为创建该项目的人
username：提交该次 pull request 的人
reponame：项目名
shas：为当前 pull request 中包含的所有 commit hash 值
modificationLines：为当前 push 中包含的所有变更代码行数（增、删代码行数）

**输出**：json 格式输出 - {status:int, message:string}；status 200 为成功，404 则为失败

**举例**：

- 命令：`chain trustiePullRequest sulenn qiubing15 "{\"source_branch\":\"Master\",\"target_branch\":\"Master\",\"source_project_id\":23,\"target_project_id\":24,\"ownername\":\"sulenn\",\"username\":\"sulenn\",\"reponame\":\"trustie1\",\"shas\":[\"f1b965795dbfb73af9c446d5c158254a955b1d28\",\"46f68a7252ba0af072e634295b61f39407ceea01\"],\"modificationLines\":5}"`

- 输出：`{ status: 200, message: 'trustiePullRequest 调用成功' }`

### query

查询某个用户在某个项目中的积分值

**命令参数**：`chain query ownername reponame username`

其中 ownername 为创建项目的人；reponame 为项目名；username 为待查询的用户

**输出**：json 格式输出 - {status:int, message:string, value:int}；status 200 为成功，404 则为失败

**举例**：

- 命令：`chain query sulenn qiubing15 sulenn`

- 输出：`{ status: 200, message: '获取 sulenn 用户余额查询结果为：49', value: 49 }`

### getAllInfo

获取某一个项目中所有用户的积分值

**命令参数**：`chain getAllInfo ownername reponame`

其中 ownername 为创建项目的人；reponame 为项目名

**输出**：json 格式输出 - {status:int, message:string, value:jsonObject}；status 200 为成功，404 则为失败；value 为一个 json 对象

**举例**：

- 命令：`chain getAllInfo sulenn qiubing15`

- 输出：`{ status: 200,
  message: '成功获得项目 qiubing15 中所有的用户余额信息',
  value: '{"sulen":21,"sulenn":49,"sulennn":20,"sulennnnn":10}' }`

### getContributionPercent

获取用户在某一个项目中的积分占比

**命令参数**：`chain getContributionPercent ownername reponame username`

其中 ownername 为创建项目的人；reponame 为项目名；username 为带查询的用户名

**输出**：json 格式输出 - {status:int, message:string, percent:int, allTokenSum:int, personalTokens:int}；

- status： 200 为成功，404 则为失败

- percent：为用户积分占比

- allTokenSum：项目总积分值

- personalTokens：用户积分值

**举例**：

- 命令：`chain getContributionPercent sulenn qiubing15 sulenn`

- 输出：`{ status: 200,
  message: 'sulenn 在 qiubing15 项目中的贡献占比为：0.49',
  percent: 0.49,
  allTokenSum: 100,
  personalTokens: 49 }`

### minusToken

减少某一个用户的积分值，用于悬赏 issue 时扣除悬赏人积分

**命令参数**：`chain minusToken ownername reponame username tokens`

其中 ownername 为创建项目的人；reponame 为项目名；username 为待查询的用户；tokens 为待扣除的积分值

**输出**：json 格式输出 - {status:int, message:string}；status 200 为成功，404 则为失败

**举例**：

- 命令：`chain minusToken sulenn qiubing15 sulenn 5`

- 输出：`{ status: 200, message: '成功从用户 sulenn 账号中扣除 5 个积分' }`

### addToken

增加某一个用户的积分值，用于悬赏 issue 被解决时，奖励解决该 issue 的用户

**命令参数**：`chain addToken ownername reponame username tokens`

其中 ownername 为创建项目的人；reponame 为项目名；username 为待查询的用户；tokens 为待增加的积分值

**输出**：json 格式输出 - {status:int, message:string}；status 200 为成功，404 则为失败

**举例**：

- 命令：`chain addToken sulenn qiubing15 sulenn 5`

- 输出：`{ status: 200, message: '成功为用户 sulenn 账号新增 5 个积分' }`
