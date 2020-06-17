# Recover 方案

**背景**：FISCO BCOS 支持的 [PBFT 共识算法](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/design/consensus/pbft.html) 需要区块链网络中至少存在 `2*f+1` 个共识节点正常工作（假定总节点数为 `3*f+1` ）才能维持网络的良性运转。但是实际生产环境中往往会出现各种特殊情况，如网络脑裂、节点网络中断、节点硬件崩溃，从而导致网络中节点数量少于 `2*f+1`，这时网络将无法对交易和区块达成共识，网络陷入瘫痪。

**目标**：处理 FISCO BCOS 区块链网络中由于节点数量不足 `2*f+1` 而引起的 PBFT 共识问题。

**解决方案**：手动修改正常共识节点数据库 `_sys_consensus_` 表中的异常共识节点信息，将异常共识节点的 type 字段值从 sealer 修改为 observer，也就是手动将异常共识节点修改为观察者节点，然后重启正常共识节点。该方案目前只支持 mysql 存储。

**处理步骤**：假设网络中存在A、B、C、D四个共识节点，C、D节点由于某些原因发生崩溃，导致网络无法正常共识打包出块。

1. **手动修改数据库**：修改节点A和节点B数据库中 `_sys_consensus_` 表，将节点C和节点D的 type 字段值从 sealer 修改为 observer；
2. **重启节点A和节点B**：重启节点之后，网络中共识节点只有节点A和节点B，满足 PBFT 共识条件，网络可正常共识打包出块；
3. **再次修改数据库表**：修改节点C和节点D中数据库  `_sys_consensus_` 表，将节点C和节点D的 type 字段值从 sealer 修改为 observer；
4. **启动节点C和节点D**：节点C和节点D作为观察者节点，可以顺利同步区块，但无法参与网络共识；
5. **添加共识节点**：通过控制台调用 addSealer 命令，将节点C和节点D转换为共识节点。

> 注意：该解决方案会引入一个新的问题。
>
> 当对现有网络进行共识节点扩容时，新加入的共识节点在同步区块的过程中会在某一个区块高度出现同步失败的情况，此时需要手动将新节点数据库 `_sys_consensus_` 表中节点C和节点D的 type 字段值从 sealer 修改为 observer，然后重启新加入的共识节点。重启后共识节点将继续同步区块，当同步的区块高度追上网络中最新的区块高度时，新加入的共识节点方可参与网络共识。

## 手动修改数据库表

为了可以让由节点A和节点B组成的网络可以继续共识，需要手动修改节点A和节点B数据库中 `_sys_consensus_` 表的 type 字段，将该字段从 sealer 修改为 observer。

1. 查看节点A和节点B的 node_id


```shell
# 节点A编号
> cat ./node0/conf/node.nodeid
060a3dc076c57e42dabb9a54d5d0460dae97f1d7f63a5f4d24f4c126f7ee5d5c2f0ced34f7a661b59fe10962f26dd2f3433f6b127c860eb0a029ad3afeb05e30

# 节点B编号
> cat ./node1/conf/node.nodeid
205d9fdeb3e3598bb6e50b4f54dab64841187cb3f8fa7f1eac71dd45fda07b81d3615857891fcd8c7cdcbb2374d8a5df0c2009d80f40e7d83db350532bc2659d
```

2. 修改节点A和节点B数据库表 `_sys_consensus_` 

```shell
# 登录 mysql
> mysql -u username -p password  # 根据实际情况填写 username 和 password

# 选择数据库
> use database-name # 根据实际情况填写 database-name

# 查看 _sys_consensus_ 表信息
> select * from _sys_consensus_;
+--------+-------+----------+------+----------+----------------------------------------------------------------------------------------------------------------------------------+------------+
| _id_   | _num_ | _status_ | name | type     | node_id                                                                                                                          | enable_num |
+--------+-------+----------+------+----------+----------------------------------------------------------------------------------------------------------------------------------+------------+
| 100004 |     0 |        0 | node | sealer   | 060a3dc076c57e42dabb9a54d5d0460dae97f1d7f63a5f4d24f4c126f7ee5d5c2f0ced34f7a661b59fe10962f26dd2f3433f6b127c860eb0a029ad3afeb05e30 | 0          |
| 100005 |     0 |        0 | node | sealer | 17f4ad3e71418be3e074c897c73fdd07a3a69a20524476dfbd4dc18fc6284714acf58d753bbc651549f7b5e30bfd82b84fdc5ac44e67aab8c1299ef92fd4e30e | 0          |
| 100006 |     0 |        0 | node | sealer   | 205d9fdeb3e3598bb6e50b4f54dab64841187cb3f8fa7f1eac71dd45fda07b81d3615857891fcd8c7cdcbb2374d8a5df0c2009d80f40e7d83db350532bc2659d | 0          |
| 100007 |     6 |        0 | node | sealer   | da8688893fcf5b4194e4da1b79e77e81be9cb740fb373e0f59bf3d28a349e7fba9b79ec36e05715fcbe7dbb68cae661d451893128e5ccf81bc1eef7b32163496 | 6          |
+--------+-------+----------+------+----------+----------------------------------------------------------------------------------------------------------------------------------+------------+
4 rows in set (0.00 sec)

# 将节点C和节点D的 type 字段修改为 observer
> update _sys_consensus_ set type="observer" where _id_="100005";
> update _sys_consensus_ set type="observer" where _id_="100007";

# 再次查看 _sys_consensus_  表信息
> select * from _sys_consensus_;
+--------+-------+----------+------+----------+----------------------------------------------------------------------------------------------------------------------------------+------------+
| _id_   | _num_ | _status_ | name | type     | node_id                                                                                                                          | enable_num |
+--------+-------+----------+------+----------+----------------------------------------------------------------------------------------------------------------------------------+------------+
| 100004 |     0 |        0 | node | sealer   | 060a3dc076c57e42dabb9a54d5d0460dae97f1d7f63a5f4d24f4c126f7ee5d5c2f0ced34f7a661b59fe10962f26dd2f3433f6b127c860eb0a029ad3afeb05e30 | 0          |
| 100005 |     0 |        0 | node | observer | 17f4ad3e71418be3e074c897c73fdd07a3a69a20524476dfbd4dc18fc6284714acf58d753bbc651549f7b5e30bfd82b84fdc5ac44e67aab8c1299ef92fd4e30e | 0          |
| 100006 |     0 |        0 | node | sealer   | 205d9fdeb3e3598bb6e50b4f54dab64841187cb3f8fa7f1eac71dd45fda07b81d3615857891fcd8c7cdcbb2374d8a5df0c2009d80f40e7d83db350532bc2659d | 0          |
| 100007 |     6 |        0 | node | observer   | da8688893fcf5b4194e4da1b79e77e81be9cb740fb373e0f59bf3d28a349e7fba9b79ec36e05715fcbe7dbb68cae661d451893128e5ccf81bc1eef7b32163496 | 6          |
+--------+-------+----------+------+----------+----------------------------------------------------------------------------------------------------------------------------------+------------+
4 rows in set (0.00 sec)

# 继续修改另外一个节点的数据库表

```

##  重启节点A和节点B

数据库中节点信息修改之后如果不重启节点A和节点B，那么节点A和节点B会读取缓存中的配置信息，网络仍旧无法正常共识。所以重启节点A和节点B，以便可以从数据库中读取新的节点配置信息。

```shell
# 重启节点A
bash ./node0/stop.sh && bash ./node0/start.sh

# 重启节点B
bash ./node1/stop.sh && bash ./node1/start.sh
```

通过查看节点A的日志发现网络共识已恢复正常

## 再次修改数据库表

如果此时将节点C和节点D启动，因为节点C和节点D数据库表中记录的共识节点包括A、B、C、D，所以节点C和节点D无法同步网络中共识产生的新区块，也无法参与到新区快的共识中。为此，用户需手动修改节点C和节点D数据库中 `_sys_consensus_` 表的字段，将节点C和节点D的 type 字段值从 sealer 修改为 observer

修改步骤同 **手动修改数据库表** 小节，不重复占用篇幅

## 启动节点C和节点D

再次启动节点C和节点D之后，节点C和节点D作为观察者节点可顺利同步区块，但是无法参与区块共识。

```shell
# 重启节点C
bash ./node2/start.sh

# 重启节点D
bash ./node3/start.sh
```

## 添加共识节点

现在节点C和节点D是观察者节点，只能同步区块却无法参与区块共识。为此，可通过在控制台执行 addSealer 命令将节点C和节点D转换为共识节点。关于节点角色转换可参考：[组员管理](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/manual/node_management.html)。

1. 查看节点C和节点D的 node_id

```shell
# 节点C编号
> cat ./node2/conf/node.nodeid
da8688893fcf5b4194e4da1b79e77e81be9cb740fb373e0f59bf3d28a349e7fba9b79ec36e05715fcbe7dbb68cae661d451893128e5ccf81bc1eef7b32163496

# 节点D编号
> cat ./node3/conf/node.nodeid
17f4ad3e71418be3e074c897c73fdd07a3a69a20524476dfbd4dc18fc6284714acf58d753bbc651549f7b5e30bfd82b84fdc5ac44e67aab8c1299ef92fd4e30e
```

2. 转换节点C和节点D为共识节点

```shell
# 转换节点C为共识节点
[group:1]> addSealer da8688893fcf5b4194e4da1b79e77e81be9cb740fb373e0f59bf3d28a349e7fba9b79ec36e05715fcbe7dbb68cae661d451893128e5ccf81bc1eef7b32163496
{
    "code":0,
    "msg":"success"
}

# 转换节点D为共识节点
[group:1]> addSealer 17f4ad3e71418be3e074c897c73fdd07a3a69a20524476dfbd4dc18fc6284714acf58d753bbc651549f7b5e30bfd82b84fdc5ac44e67aab8c1299ef92fd4e30e
{
    "code":0,
    "msg":"success"
}
```







