[toc]

# FISCO BCOS

- Repo Table

  | TokenName                                                    | Owner              | TotalSupply        | CurSupply            |
  | ------------------------------------------------------------ | ------------------ | ------------------ | -------------------- |
  | string，token代号（唯一），即币种，如bitcoin（比特币）、ether（以太币） | string，项目创始人 | int，项目总token数 | int，项目当前token数 |

- User Balance Table

  | User           | TokenName                 | Balance            |
  | -------------- | ------------------------- | ------------------ |
  | string，用户名 | string，token代号（唯一） | int，用户token余额 |

- 区块链网络：单服务器四节点

- 服务器ip和port：106.54.84.172:6000

## 接口

*统一请求接口：*/trustie/fiscobcos

### 项目相关

1. 创建项目

   **参数**：

   ```json
   {"request-type":"create repo","username":"sulenn1","total_supply":1000000,"token_name":"pdl0","token_balance":[["qiubing1", 1000],["sulen",100]]}
   ```

   **响应**：

   ```json
   {
       "status": 0,
       "message": "create repo successfully!"
   }
   ```

2. 查询项目基本信息

   **参数**：

   ```json
   {"request-type":"query repo basic info","token_name":"pdl0"}
   ```

   **响应**：

   ```json
   {
       "username": "sulenn1",
       "token_name": "pdl4",
       "total_supply": 1000000,
       "cur_supply": 1100,
       "status": 0,
       "message": "query success!"
   }
   ```

### 用户相关

1. 查询用户单个项目余额

   **参数**：

   ```json
   {"request-type":"query user balance of single repo","username":"qiubing1", "token_name":"pdl0"}
   ```

   **响应**：

   ```json
   {
       "username": "qiubing1",
       "token_name": "pdl3",
       "balance": 1000,
       "status": 0,
       "message": "query success!"
   }
   ```

2. 查询用户所有项目余额

   **参数**：

   ```json
   {"request-type":"query user balance of all repos","username":"qiubing1"}
   ```

   

   **响应**：

   ```json
   {
       "status": 0,
       "message": "query success!",
       "UserBalanceList": [
           {
               "username": "qiubing1",
               "token_name": "pdl6",
               "balance": 1000
           },
           {
               "username": "qiubing1",
               "token_name": "pdl0",
               "balance": 1000
           },
           {
               "username": "qiubing1",
               "token_name": "pdl1",
               "balance": 1000
           }
       ]
   }
   ```


3. 新增用户某一项目余额

   **参数**：

   ```json
   {"request-type":"add user balance","username":"qiubing1", "token_name":"pdl4", "amount":500}
   ```

   **响应**：

   ```json
   {
       "status": 0,
       "message": "user adds amount successfully!"
   }
   ```

4. 减少用户某一项目余额

   **参数**：

   ```json
   {"request-type":"minus user balance","username":"qiubing1", "token_name":"pdl8", "amount":50}
   ```

   **响应**：

   ```json
   {
       "status": 0,
       "message": "user minus amount successfully!"
   }
   ```

   

5. 用户转账

   **参数**：

   ```json
   {"request-type":"transfer amount","payer":"qiubing1", "payee":"qiubing2", "token_name":"pdl8", "amount":50}
   ```

   **响应**：

   ```json
   {
       "status": 0,
       "message": "user transfers amount successfully!"
   }
   ```

6. 用户余额锁定

   **参数**：

   ```json
   {"request-type":"lock user balance","username":"qiubing1", "token_name":"pdl4", "amount":50}
   ```

   **响应**：

   ```json
   {
       "status": 0,
       "message": "user locks amount successfully!"
   }
   ```

7. 用户余额解锁

   **参数**：

   ```json
   {"request-type":"unlock user balance","username":"qiubing1", "token_name":"pdl4", "amount":50}
   ```

   **响应**：

   ```json
   {
       "status": 0,
       "message": "user unlocks amount successfully!"
   }
   ```


### 存证确权

1. commit 数据

   - 存证

     **参数**：

       ```json
       {"request-type":"upload commit info","commit_hash":"3f2dc2ec21876d31ffa06744f215b6a17c5d3bad","repo_id":"1002604","author":"sulenn","email":"273409891@qq.com","time":"Mon Aug 31 23:24:28 2020 +0800","content":"fix dead lock when use AMOP broadcast with FISCO BCOS v2.6 (#71)","commit_diff":"commit3f2dc2ec21876d31ffa06744f215b6a17c5d3badAuthor:sulenn<273409891@qq.com>Date:MonAug3123:24:282020+0800fixdeadlockwhenuseAMOPbroadcastwithFISCOBCOSv2.6(#71)diff--gita/conn/channel.gob/conn/channel.goindex4882200..f5384d4100644---a/conn/channel.go+++b/conn/channel.go@@-1035,7+1035,11@@func(hc*channelSession)processMessages(){hc.mu.Lock()ifresponse,ok:=hc.responses[msg.uuid];ok{response.Message=msg-response.Notify<-struct{}{}+ifresponse.Notify!=nil{+response.Notify<-struct{}{}+close(response.Notify)+}+response.Notify=nil}hc.mu.Unlock()switchmsg.typeN{"}
       ```

      ​**响应**：

       ```json
       {
           "status": 0,
           "message": "commit upload successfully!"
       }
       ```

   - 确权

     **参数**：

     ```json
     {"request-type":"query commit info","commit_hash":"3f2dc2ec21876d31ffa06744f215b6a17c5d3bad"}
     ```

     **响应**：
     
     ```json
     {
         "commit_hash": "3f2dc2ec21876d31ffa06744f215b6a17c5d3bad",
         "repo_id": "1002604",
         "author": "sulenn",
         "email": "273409891@qq.com",
         "time": "Mon Aug 31 23:24:28 2020 +0800",
         "content": "fix dead lock when use AMOP broadcast with FISCO BCOS v2.6 (#71)",
         "commit_diff": "commit3f2dc2ec21876d31ffa06744f215b6a17c5d3badAuthor:sulenn<273409891@qq.com>Date:MonAug3123:24:282020+0800fixdeadlockwhenuseAMOPbroadcastwithFISCOBCOSv2.6(#71)diff--gita/conn/channel.gob/conn/channel.goindex4882200..f5384d4100644---a/conn/channel.go+++b/conn/channel.go@@-1035,7+1035,11@@func(hc*channelSession)processMessages(){hc.mu.Lock()ifresponse,ok:=hc.responses[msg.uuid];ok{response.Message=msg-response.Notify<-struct{}{}+ifresponse.Notify!=nil{+response.Notify<-struct{}{}+close(response.Notify)+}+response.Notify=nil}hc.mu.Unlock()switchmsg.typeN{",
         "status": 0,
         "message": "query success!"
     }
     ```

   2. push 数据

      - 存证

        **参数**：

        ```json
        {"request-type":"upload push info","push_id":"trustie-00000001","push_number":1,"repo_id":"1002604","reponame":"go-sdk","ownername":"qiubing","username":"sulenn","branch":"Master", "size":3, "commit_shas":["c901d1fc2400f35d8c64c03e58e8e3ea916adec9","1093c5844458ad35e9c69f5eb6c53c11a4380c21","de370565bebb692aee44c67761317e7600c853f4"],"time":"Fri Sep 11 19:54:22 2020 +0800"}
        ```

        **响应**：

        ```json
        {
            "status": 0,
            "message": "push upload successfully!"
        }
        ```

      - 确权

        **参数**：

        ```json
        {"request-type":"query push info","push_id":"trustie-00000001"}
        ```

        **响应**：

        ```json
        {
            "push_Id": "trustie-00000001",
            "push_number": 1,
            "reponame": "go-sdk",
            "ownername": "qiubing",
            "branch": "Master",
            "commit_shas": [
                "c901d1fc2400f35d8c64c03e58e8e3ea916adec9",
                "1093c5844458ad35e9c69f5eb6c53c11a4380c21",
                "de370565bebb692aee44c67761317e7600c853f4"
            ],
            "repo_id": "1002604",
            "username": "sulenn",
            "size": 3,
            "time": "Fri Sep 11 19:54:22 2020 +0800",
            "status": 0,
            "message": "query success!"
        }
        ```

   3. pull request 数据

      - 存证

        **参数**：

        ```json
        {"request-type":"upload pull request info","pull_request_id":"trustie-00000002","pull_request_number":1,"repo_id":"1002604","reponame":"go-sdk","ownername":"qiubing","username":"sulenn","action":"opened","title":"Add resource to Naming Routes override example","content":"Added a resource to the Naming Routes override example. Took me a while to realize that order matters, I figure having the resource in the example will help other newbies.","source_branch":"develop","target_branch":"develop","reviewers":["lebron","james"],"commit_shas":["c901d1fc2400f35d8c64c03e58e8e3ea916adec9","1093c5844458ad35e9c69f5eb6c53c11a4380c21","de370565bebb692aee44c67761317e7600c853f4"],"merge_user":"kobe","created_at":"Wed Sep 9 22:42:48 2020 +0800","updated_at":"Fri Sep 11 19:54:22 2020 +0800"}
        ```

        **响应**：

        ```json
        {
            "status": 0,
            "message": "pull request upload successfully!"
        }
        ```

      - 确权最新数据

        **参数**：

        ```json
        {"request-type":"query pull request latest info","pull_request_id":"trustie-00000002"}
        ```

        **响应**：

        ```json
        {
            "pull_request_id": "trustie-00000002",
            "pull_request_number": 1,
            "action": "opened",
            "title": "Add resource to Naming Routes override example",
            "source_branch": "develop",
            "target_repo_id": "",
            "target_branch": "develop",
            "reviewers": [
                "lebron",
                "james"
            ],
            "merge_user": "kobe",
            "created_at": "Wed Sep 9 22:42:48 2020 +0800",
            "updated_ad": "Fri Sep 11 19:54:22 2020 +0800",
            "repo_id": "1002604",
            "reponame": "go-sdk",
            "ownername": "qiubing",
            "username": "sulenn",
            "content": "Added a resource to the Naming Routes override example. Took me a while to realize that order matters, I figure having the resource in the example will help other newbies.",
            "commit_shas": [
                "c901d1fc2400f35d8c64c03e58e8e3ea916adec9",
                "1093c5844458ad35e9c69f5eb6c53c11a4380c21",
                "de370565bebb692aee44c67761317e7600c853f4"
            ],
            "status": 0,
            "message": "query success!"
        }
        ```

      - 确权历史所有数据

        **参数**：

        ```json
        {"request-type":"query pull request all info","pull_request_id":"trustie-00000002"}
        ```

        **响应**：

        ```json
        {
            "UploadPullReuqestOptionArr": [
                {
                    "pull_request_id": "trustie-00000002",
                    "pull_request_number": 1,
                    "action": "opened",
                    "title": "Add resource to Naming Routes override example",
                    "source_branch": "develop",
                    "target_repo_id": "",
                    "target_branch": "develop",
                    "reviewers": [
                        "lebron",
                        "james"
                    ],
                    "merge_user": "kobe",
                    "created_at": "Wed Sep 9 22:42:48 2020 +0800",
                    "updated_ad": "Fri Sep 11 19:54:22 2020 +0800",
                    "repo_id": "1002604",
                    "reponame": "go-sdk",
                    "ownername": "qiubing",
                    "username": "sulenn",
                    "content": "Added a resource to the Naming Routes override example. Took me a while to realize that order matters, I figure having the resource in the example will help other newbies.",
                    "commit_shas": [
                        "c901d1fc2400f35d8c64c03e58e8e3ea916adec9",
                        "1093c5844458ad35e9c69f5eb6c53c11a4380c21",
                        "de370565bebb692aee44c67761317e7600c853f4"
                    ]
                },
                {
                    "pull_request_id": "trustie-00000002",
                    "pull_request_number": 1,
                    "action": "opened",
                    "title": "Add resource to Naming Routes override example",
                    "source_branch": "develop",
                    "target_repo_id": "",
                    "target_branch": "develop",
                    "reviewers": [
                        "lebron",
                        "james"
                    ],
                    "merge_user": "kobe",
                    "created_at": "Wed Sep 9 22:42:48 2020 +0800",
                    "updated_ad": "Fri Sep 11 19:54:22 2020 +0800",
                    "repo_id": "1002604",
                    "reponame": "go-sdk",
                    "ownername": "qiubing",
                    "username": "sulenn",
                    "content": "Added a resource to the Naming Routes override example. Took me a while to realize that order matters, I figure having the resource in the example will help other newbies.",
                    "commit_shas": [
                        "c901d1fc2400f35d8c64c03e58e8e3ea916adec9",
                        "1093c5844458ad35e9c69f5eb6c53c11a4380c21",
                        "de370565bebb692aee44c67761317e7600c853f4"
                    ]
                }
            ],
            "status": 0,
            "message": "query success!"
        }
        ```

   4. pull request comment 数据

      - 存证

        **参数**：

        ```json
        {"request-type":"upload pull request comment info","pull_request_comment_id":"trustie-00000002","pull_request_id":"trustie-00000002","pull_request_comment_number":1,"pull_request_number":1,"repo_id":"00000001","parent_id":"00000001","reponame":"go-sdk","ownername":"qiubing","username":"sulenn","action":"created","content":"nice tip!","created_at":"Wed Sep 9 22:42:48 2020 +0800","updated_at":"Fri Sep 11 19:54:22 2020 +0800"}
        ```

        **确权**：

        ```json
        {
            "status": 0,
            "message": "pull request comment upload successfully!"
        }
        ```

      - 确权最新数据

        **参数**：

        ```json
        {"request-type":"query pull request comment latest info","pull_request_comment_id":"trustie-00000002"}
        ```

        **响应**：

        ```json
        {
            "pull_request_comment_id": "trustie-00000002",
            "pull_request_comment_number": 1,
            "parent_id": "00000001",
            "pull_request_number": 1,
            "pull_request_id": "trustie-00000002",
            "repo_id": "00000001",
            "reponame": "go-sdk",
            "ownername": "qiubing",
            "username": "sulenn",
            "action": "created",
            "content": "nice tip!",
            "created_at": "Wed Sep 9 22:42:48 2020 +0800",
            "updated_at": "Fri Sep 11 19:54:22 2020 +0800",
            "status": 0,
            "message": "query success!"
        }
     ```
   
   - 确权所有历史数据
   
     **参数**：
   
        ```json
        {"request-type":"query pull request comment all info","pull_request_comment_id":"trustie-00000002"}
     ```
   
     **响应**：
   
        ```json
        {
            "UploadPullReuqestCommentOptionArr": [
                {
                    "pull_request_comment_id": "trustie-00000002",
                    "pull_request_comment_number": 1,
                    "parent_id": "00000001",
                    "pull_request_number": 1,
                    "pull_request_id": "",
                    "repo_id": "00000001",
                    "reponame": "go-sdk",
                    "ownername": "qiubing",
                    "username": "sulenn",
                    "action": "created",
                    "content": "nice tip!",
                    "created_at": "Wed Sep 9 22:42:48 2020 +0800",
                    "updated_at": "Fri Sep 11 19:54:22 2020 +0800"
                },
                {
                    "pull_request_comment_id": "trustie-00000002",
                    "pull_request_comment_number": 1,
                    "parent_id": "00000001",
                    "pull_request_number": 1,cd 
                    "pull_request_id": "trustie-00000002",
                    "repo_id": "00000001",
                    "reponame": "go-sdk",
                    "ownername": "qiubing",
                    "username": "sulenn",
                    "action": "created",
                    "content": "nice tip!",
                    "created_at": "Wed Sep 9 22:42:48 2020 +0800",
                    "updated_at": "Fri Sep 11 19:54:22 2020 +0800"
                }
            ],
            "status": 0,
         "message": "query success!"
        }
     ```
   
5. issue 数据
   
   - 存证
   
        **参数**：
   
     ```json
        {"request-type":"upload issue info","issue_id":"trustie-00000002","issue_number":1,"repo_id":"00000001","reponame":"go-sdk","ownername":"qiubing","username":"sulenn","action":"opened","title":"Issue creating fixtures ActiveRecord::FixtureSet","content":"after update from rails 5 to 6 and only running rspec","created_at":"Wed Sep 9 22:42:48 2020 +0800","updated_at":"Fri Sep 11 19:54:22 2020 +0800"}
     ```
   
        **响应**：
   
        ```json
        {
            "status": 0,
         "message": "issue upload successfully!"
        }
     ```
   
   - 确权最新数据
   
        **参数**：
   
     ```json
        {"request-type":"query issue latest info","issue_id":"trustie-00000002"}
     ```
   
        **响应**：
   
        ```json
        {
            "issue_id": "trustie-00000002",
            "issue_number": 1,
            "repo_id": "00000001",
            "reponame": "go-sdk",
            "ownername": "qiubing",
            "username": "sulenn",
            "action": "opened",
            "title": "Issue creating fixtures ActiveRecord::FixtureSet",
            "content": "after update from rails 5 to 6 and only running rspec",
            "created_at": "Wed Sep 9 22:42:48 2020 +0800",
            "updated_at": "Fri Sep 11 19:54:22 2020 +0800",
            "status": 0,
         "message": "query success!"
        }
     ```
   
   - 确权所有历史数据
   
        **参数**：
   
     ```json
        {"request-type":"query issue all info","issue_id":"trustie-00000002"}
     ```
   
        **响应**：
   
        ```json
        {
            "UploadIssueOptionArr": [
                {
                    "issue_id": "trustie-00000002",
                    "issue_number": 1,
                    "repo_id": "00000001",
                    "reponame": "go-sdk",
                    "ownername": "qiubing",
                    "username": "sulenn",
                    "action": "opened",
                    "title": "Issue creating fixtures ActiveRecord::FixtureSet",
                    "content": "after update from rails 5 to 6 and only running rspec",
                    "created_at": "Wed Sep 9 22:42:48 2020 +0800",
                    "updated_at": "Fri Sep 11 19:54:22 2020 +0800"
                },
                {
                    "issue_id": "trustie-00000002",
                    "issue_number": 1,
                    "repo_id": "00000001",
                    "reponame": "go-sdk",
                    "ownername": "qiubing",
                    "username": "sulenn",
                    "action": "opened",
                    "title": "Issue creating fixtures ActiveRecord::FixtureSet",
                    "content": "after update from rails 5 to 6 and only running rspec",
                    "created_at": "Wed Sep 9 22:42:48 2020 +0800",
                    "updated_at": "Fri Sep 11 19:54:22 2020 +0800"
                }
            ],
            "status": 0,
         "message": "query success!"
        }
     ```
   
6. issue comment 数据
   
   - 存证
   
        **参数**：
   
     ```json
        {"request-type":"upload issue comment info","issue_comment_id":"trustie-00000002","issue_id":"trustie-00000002","issue_comment_number":1,"issue_number":1,"repo_id":"00000001","parent_id":"00000001","reponame":"go-sdk","ownername":"qiubing","username":"sulenn","action":"created","content":"nice tip!","created_at":"Wed Sep 9 22:42:48 2020 +0800","updated_at":"Fri Sep 11 19:54:22 2020 +0800"}
     ```
   
        **响应**：
   
        ```json
        {
            "status": 0,
         "message": "issue comment upload successfully!"
        }
     ```
   
   - 确权最新数据
   
        **参数**：
   
     ```json
        {"request-type":"query issue comment latest info","issue_comment_id":"trustie-00000002"}
     ```
   
        **响应**：
   
        ```json
        {
            "issue_comment_id": "trustie-00000002",
            "issue_comment_number": 1,
            "issue_number": 1,
            "issue_id": "trustie-00000002",
            "repo_id": "00000001",
            "parent_id": "00000001",
            "reponame": "go-sdk",
            "ownername": "qiubing",
            "username": "sulenn",
            "action": "created",
            "content": "nice tip!",
            "created_at": "Wed Sep 9 22:42:48 2020 +0800",
            "updated_at": "Fri Sep 11 19:54:22 2020 +0800",
         "status": 0,
            "message": "query success!"
     }
     ```
   
   - 确权所有历史数据
      
     **参数**：
   
        ```json
     {"request-type":"query issue comment all info","issue_comment_id":"trustie-00000002"}
     ```
      
     **响应**：
      
        ```json
        {
            "UploadIssueCommentOptionArr": [
                {
                    "issue_comment_id": "trustie-00000002",
                    "issue_comment_number": 1,
                    "issue_number": 1,
                    "issue_id": "",
                    "repo_id": "00000001",
                    "parent_id": "00000001",
                    "reponame": "go-sdk",
                    "ownername": "qiubing",
                    "username": "sulenn",
                    "action": "created",
                    "content": "nice tip!",
                    "created_at": "Wed Sep 9 22:42:48 2020 +0800",
                    "updated_at": "Fri Sep 11 19:54:22 2020 +0800"
                },
                {
                    "issue_comment_id": "trustie-00000002",
                    "issue_comment_number": 1,
                    "issue_number": 1,
                    "issue_id": "trustie-00000002",
                    "repo_id": "00000001",
                    "parent_id": "00000001",
                    "reponame": "go-sdk",
                    "ownername": "qiubing",
                    "username": "sulenn",
                    "action": "created",
                    "content": "nice tip!",
                    "created_at": "Wed Sep 9 22:42:48 2020 +0800",
                    "updated_at": "Fri Sep 11 19:54:22 2020 +0800"
                }
            ],
            "status": 0,
            "message": "query success!"
        }
        ```
        
        

