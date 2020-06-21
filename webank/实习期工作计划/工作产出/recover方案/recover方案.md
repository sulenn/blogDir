# 极端异常下的共识恢复应急方案

**背景**：FISCO BCOS 支持的 [PBFT 共识算法](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/design/consensus/pbft.html) 需要区块链网络中至少存在 `2*f+1` 个共识节点正常工作（假定总节点数为 `3*f+1` ）才能维持网络的良性运转。但是实际生产环境中往往会出现各种特殊情况，如网络脑裂、节点网络中断、节点硬件崩溃，从而导致网络中节点数量少于 `2*f+1`，这时网络将无法对交易和区块达成共识，网络陷入瘫痪。

**目标**：处理 FISCO BCOS 区块链网络中由于节点数量不足 `2*f+1` 而引起的 PBFT 网络共识异常问题。

**解决方案**：手动关闭正常共识节点，修改正常共识节点数据库 `_sys_consensus_` 表中的异常共识节点信息，将异常共识节点的 type 字段值从 sealer 修改为 observer，也就是手动将异常共识节点修改为观察者节点，然后启动正常共识节点。

FISCO BCOS 支持不同的数据库存储模式，不同的数据库模式在操作和存储方面存在差异。接下来分别讲解 Mysql 和 RocksDB 数据库存储模式在面对 PBFT 共识节点不足的情况下可采取的处理方案。

## Mysql存储模式

**处理步骤**：假设网络中存在A、B、C、D四个共识节点，C、D节点由于某些原因发生崩溃，导致网络无法正常共识打包出块。

1. **关闭节点A和节点B**：为了防止操作数据库给正在运行的网络造成影响，建议先关闭节点A和节点B；
2. **手动修改节点A和节点B的数据库**：确保A、B、C、D节点块高一致的前提下修改节点A和节点B数据库中 `_sys_consensus_` 表，将节点C和节点D的 type 字段值从 sealer 修改为 observer；
3. **启动节点A和节点B**：重启节点之后，网络中共识节点只有节点A和节点B，满足 PBFT 共识条件，网络可正常共识打包出块；
4. **手动修改节点C和节点D的数据库**：修改节点C和节点D中数据库  `_sys_consensus_` 表，将节点C和节点D的 type 字段值从 sealer 修改为 observer；
5. **启动节点C和节点D**：节点C和节点D作为观察者节点，可以顺利同步区块，但无法参与网络共识；
6. **添加共识节点**：通过控制台调用 addSealer 命令，将节点C和节点D转换为共识节点。

> 注意：该解决方案会引入一个新的问题。
>
> 当对现有网络进行共识节点扩容时，新加入的共识节点在同步区块的过程中会在某一个区块高度出现同步失败的情况，此时需要手动将新节点数据库 `_sys_consensus_` 表中节点C和节点D的 type 字段值从 sealer 修改为 observer，然后重启新加入的共识节点。重启后共识节点将继续同步区块，当同步的区块高度追上网络中最新的区块高度时，新加入的共识节点方可参与网络共识。

### 关闭节点A和节点B

网络在运行的过程中直接修改数据库表中字段值是非常危险的，所以先关闭节点A和节点B。

```shell
# 关闭节点A
bash ./node0/stop.sh

# 关闭节点B
bash ./node1/stop.sh
```

### 手动修改节点A和节点B的数据库

在A、B、C、D节点拥有相同的最新区块高度前提下，为了可以让由节点A和节点B组成的网络可以继续共识，需要手动修改节点A和节点B数据库中 `_sys_consensus_` 表的 type 字段，将该字段值从 sealer 修改为 observer。

1. 查看节点C和节点D的 node_id


```shell
# 节点C编号
> cat ./node2/conf/node.nodeid
da8688893fcf5b4194e4da1b79e77e81be9cb740fb373e0f59bf3d28a349e7fba9b79ec36e05715fcbe7dbb68cae661d451893128e5ccf81bc1eef7b32163496

# 节点D编号
> cat ./node3/conf/node.nodeid
17f4ad3e71418be3e074c897c73fdd07a3a69a20524476dfbd4dc18fc6284714acf58d753bbc651549f7b5e30bfd82b84fdc5ac44e67aab8c1299ef92fd4e30e
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

### 启动节点A和节点B

启动节点A和节点B，从数据库中读取新的节点配置信息。通过查看节点A的日志发现网络共识已恢复正常。

```shell
# 启动节点A
bash ./node0/start.sh

# 启动节点B
bash ./node1/start.sh
```

### 手动修改节点C和节点D的数据库

如果此时将节点C和节点D启动，因为节点C和节点D数据库表中记录的共识节点包括A、B、C、D，所以节点C和节点D无法同步网络中共识产生的新区块，也无法参与到新区块的共识中。为此，用户需手动修改节点C和节点D数据库中 `_sys_consensus_` 表的字段，将节点C和节点D的 type 字段值从 sealer 修改为 observer。

修改步骤同 **手动修改节点A和节点B的数据库** 小节，不重复占用篇幅。

### 启动节点C和节点D

再次启动节点C和节点D之后，节点C和节点D作为观察者节点可顺利同步区块，但是无法参与区块共识。

```shell
# 启动节点C
bash ./node2/start.sh

# 启动节点D
bash ./node3/start.sh
```

### 添加共识节点

现在节点C和节点D是观察者节点，只能同步区块却无法参与区块共识。为此，可通过在控制台执行 addSealer 命令将节点C和节点D转换为共识节点。关于节点角色转换可参考：[组员管理](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/manual/node_management.html)。

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

## RocksDB存储模式

**处理步骤**：假设网络中存在A、B、C、D四个共识节点，C、D节点由于某些原因发生崩溃，导致网络无法正常共识打包出块。

1. **获取rocksdb-storage工具**：编译 [FISCO BCOS源码](https://github.com/FISCO-BCOS/FISCO-BCOS) 获取 rocksdb-storage 工具，使用该工具查询、修改 rocksdb 数据库中信息；
2. **关闭节点A和节点B**：为了防止操作数据库给正在运行的网络造成影响，建议先关闭节点A和节点B；
3. **手动修改节点A和节点B的数据库**：确保A、B、C、D节点块高一致的前提下修改节点A和节点B数据库中 `_sys_consensus_` 表，将节点C和节点D的 type 字段值从 sealer 修改为 observer；
4. **启动节点A和节点B**：启动节点之后，网络中共识节点只有节点A和节点B，满足 PBFT 共识条件，网络可正常共识打包出块；
5. **手动修改节点C和节点D的数据库**：修改节点C和节点D中数据库  `_sys_consensus_` 表，将节点C和节点D的 type 字段值从 sealer 修改为 observer；
6. **启动节点C和节点D**：节点C和节点D作为观察者节点，可以顺利同步区块，但无法参与网络共识；
7. **添加共识节点**：通过控制台调用 addSealer 命令，将节点C和节点D转换为共识节点。

> 注意：该解决方案会引入一个新的问题。
>
> 当对现有网络进行共识节点扩容时，新加入的共识节点在同步区块的过程中会在某一个区块高度出现同步失败的情况，此时需要手动将新节点数据库 `_sys_consensus_` 表中节点C和节点D的 type 字段值从 sealer 修改为 observer，然后重启新加入的共识节点。重启后共识节点将继续同步区块，当同步的区块高度追上网络中最新的区块高度时，新加入的共识节点方可参与网络共识。

### 获取rocksdb-storage工具

FISCO BCOS 提供查询、修改 rocksdb 数据库信息的 rocksdb-storage 工具。该工具需要手动编译 [FISCO BCOS源码](https://github.com/FISCO-BCOS/FISCO-BCOS) 获取，编译时通过 `cmake -DTOOL=on ..` 打开工具开关，编译成功后 rocksdb-storage 工具位于 `FISCO-BCOS/build/bin/`。详细编译步骤可参考：[编译](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/manual/get_executable.html#id5)。

```shell
#  编译成功后将 rocksdb-storage 工具移动到节点主目录，如 ~/fisco/nodes/127.0.0.1/
cp ./bin/rocksdb-storage ~/fisco/nodes/127.0.0.1/
```

### 关闭节点A和节点B

网络在运行的过程中直接修改数据库表中字段值是非常危险的，所以先关闭节点A和节点B。

```shell
# 关闭节点A
bash ./node0/stop.sh

# 关闭节点B
bash ./node1/stop.sh
```

### 手动修改节点A和节点B的数据库

在A、B、C、D节点拥有相同的最新区块高度前提下，为了可以让由节点A和节点B组成的网络可以继续共识，需要使用 rocksdb-storage 工具手动修改节点A和节点B数据库中 `_sys_consensus_` 表的 type 字段，将该字段值从 sealer 修改为 observer。

1. 查看节点C和节点D的 node_id


```shell
# 节点C编号
> cat ./node2/conf/node.nodeid
700076fb7e70c073dd04858f1030cfe51b482e6ec80e20cdcfa33335091f930b67620dd5b1627607195b1512816bb11cc4bc569d71708a302181a6e645b0a1c7

# 节点D编号
> cat ./node3/conf/node.nodeid
b88921fc39d90190fa908b24cbbd8282d823984e24f56a01f622c87c2d0f255e02eec03dbf427e29a56180b8dbc743ed054c43faa36df4ba1e81ae78ed0ad3aa
```

2. 修改节点A和节点B数据库表 `_sys_consensus_` 

```shell
# 使用 rocksdb-storage 工具查询节点A数据库 _sys_consensus_ 表数据
> ./rocksdb-storage -p ./node0/data/group1/block/RocksDB -s _sys_consensus_ node
DB path : ./node0/data/group1/block/RocksDB
select [_sys_consensus_,node] || params num : 2
================ open Table [_sys_consensus_] success! key node
***0 [ enable_num=0 ][ name=node ][ node_id=700076fb7e70c073dd04858f1030cfe51b482e6ec80e20cdcfa33335091f930b67620dd5b1627607195b1512816bb11cc4bc569d71708a302181a6e645b0a1c7 ][ type=sealer ]
***1 [ enable_num=0 ][ name=node ][ node_id=b64c6d23f479e4740c09662bcbf721c265abf34d511644d92344a20bb7c661de33d72cf56d3ba0d3eb4cb553cd4913dc30519351270e02af2c6bb45706ad4896 ][ type=sealer ]
***2 [ enable_num=0 ][ name=node ][ node_id=b88921fc39d90190fa908b24cbbd8282d823984e24f56a01f622c87c2d0f255e02eec03dbf427e29a56180b8dbc743ed054c43faa36df4ba1e81ae78ed0ad3aa ][ type=sealer ]
***3 [ enable_num=0 ][ name=node ][ node_id=e794f3666fb0c9d36e8f5e9a99d49bc15304a8332f3945c260bfdafd66dc04b5a6c1397e8900a835cce54b58b94d7993097b1706bc1398d5c3ddf1e002902b03 ][ type=sealer ]

# 修改节点A数据库 _sys_consensus_ 表中节点C的 type 字段值
> ./rocksdb-storage -p ./node0/data/group1/block/RocksDB -u _sys_consensus_ node node_id 700076fb7e70c073dd04858f1030cfe51b482e6ec80e20cdcfa33335091f930b67620dd5b1627607195b1512816bb11cc4bc569d71708a302181a6e645b0a1c7 type observer
DB path : ./node0/data/group1/block/RocksDB
update [_sys_consensus_,node,node_id,700076fb7e70c073dd04858f1030cfe51b482e6ec80e20cdcfa33335091f930b67620dd5b1627607195b1512816bb11cc4bc569d71708a302181a6e645b0a1c7,type,observer] || params num : 6
open Table [_sys_consensus_] success!
condition is [node_id=700076fb7e70c073dd04858f1030cfe51b482e6ec80e20cdcfa33335091f930b67620dd5b1627607195b1512816bb11cc4bc569d71708a302181a6e645b0a1c7]
update [type:observer]

# 修改节点A数据库 _sys_consensus_ 表中节点D的 type 字段值
> ./rocksdb-storage -p ./node0/data/group1/block/RocksDB -u _sys_consensus_ node node_id b88921fc39d90190fa908b24cbbd8282d823984e24f56a01f622c87c2d0f255e02eec03dbf427e29a56180b8dbc743ed054c43faa36df4ba1e81ae78ed0ad3aa type observer
DB path : ./node0/data/group1/block/RocksDB
update [_sys_consensus_,node,node_id,b88921fc39d90190fa908b24cbbd8282d823984e24f56a01f622c87c2d0f255e02eec03dbf427e29a56180b8dbc743ed054c43faa36df4ba1e81ae78ed0ad3aa,type,observer] || params num : 6
open Table [_sys_consensus_] success!
condition is [node_id=b88921fc39d90190fa908b24cbbd8282d823984e24f56a01f622c87c2d0f255e02eec03dbf427e29a56180b8dbc743ed054c43faa36df4ba1e81ae78ed0ad3aa]
update [type:observer]

# 再次查询节点A数据库 _sys_consensus_ 表数据
> ./rocksdb-storage -p ./node0/data/group1/block/RocksDB -s _sys_consensus_ node
DB path : ./node0/data/group1/block/RocksDB
select [_sys_consensus_,node] || params num : 2
================ open Table [_sys_consensus_] success! key node
***0 [ enable_num=0 ][ name=node ][ node_id=700076fb7e70c073dd04858f1030cfe51b482e6ec80e20cdcfa33335091f930b67620dd5b1627607195b1512816bb11cc4bc569d71708a302181a6e645b0a1c7 ][ type=observer ]
***1 [ enable_num=0 ][ name=node ][ node_id=b64c6d23f479e4740c09662bcbf721c265abf34d511644d92344a20bb7c661de33d72cf56d3ba0d3eb4cb553cd4913dc30519351270e02af2c6bb45706ad4896 ][ type=sealer ]
***2 [ enable_num=0 ][ name=node ][ node_id=b88921fc39d90190fa908b24cbbd8282d823984e24f56a01f622c87c2d0f255e02eec03dbf427e29a56180b8dbc743ed054c43faa36df4ba1e81ae78ed0ad3aa ][ type=observer ]
***3 [ enable_num=0 ][ name=node ][ node_id=e794f3666fb0c9d36e8f5e9a99d49bc15304a8332f3945c260bfdafd66dc04b5a6c1397e8900a835cce54b58b94d7993097b1706bc1398d5c3ddf1e002902b03 ][ type=sealer ]

# 继续修改节点B的数据库表

```

### 启动节点A和节点B

启动节点A和节点B，从数据库中读取新的节点配置信息。启动成功后，通过查看节点A的日志发现网络共识已恢复正常。

```shell
# 启动节点A
bash ./node0/start.sh

# 启动节点B
bash ./node1/start.sh
```

### 手动修改节点C和节点D的数据库

如果此时将节点C和节点D启动，因为节点C和节点D数据库表中记录的共识节点包括A、B、C、D，所以节点C和节点D无法同步网络中共识产生的新区块，也无法参与到新区块的共识中。为此，用户需手动修改节点C和节点D数据库中 `_sys_consensus_` 表的字段，将节点C和节点D的 type 字段值从 sealer 修改为 observer。

修改步骤同 **手动修改节点A和节点B的数据库** 小节，不重复占用篇幅。

### 启动节点C和节点D

启动节点C和节点D之后，节点C和节点D作为观察者节点可顺利同步区块，但是无法参与区块共识。

```shell
# 启动节点C
bash ./node2/start.sh

# 启动节点D
bash ./node3/start.sh
```

### 添加共识节点

现在节点C和节点D是观察者节点，只能同步区块却无法参与区块共识。为此，可通过在控制台执行 addSealer 命令将节点C和节点D转换为共识节点。关于节点角色转换可参考：[组员管理](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/manual/node_management.html)。

```shell
# 转换节点C为共识节点
[group:1]> addSealer 700076fb7e70c073dd04858f1030cfe51b482e6ec80e20cdcfa33335091f930b67620dd5b1627607195b1512816bb11cc4bc569d71708a302181a6e645b0a1c7
{
    "code":0,
    "msg":"success"
}

# 转换节点D为共识节点
[group:1]> addSealer b88921fc39d90190fa908b24cbbd8282d823984e24f56a01f622c87c2d0f255e02eec03dbf427e29a56180b8dbc743ed054c43faa36df4ba1e81ae78ed0ad3aa
{
    "code":0,
    "msg":"success"
}
```







