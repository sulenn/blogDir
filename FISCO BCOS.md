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

   

   