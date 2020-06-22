# rocksdb-storage 工具

FISCO BCOS 提供 rocksdb-storage 工具帮助用户对 rocksdb 数据库进行常规 CRUD 操作，此外 rocksdb-storage 工具也支持手动创建数据表。

**获取途径**：手动编译 [FISCO BCOS源码](https://github.com/FISCO-BCOS/FISCO-BCOS)。编译时通过 `cmake -DTOOL=on ..` 打开工具开关，编译成功后 rocksdb-storage 工具位于 `FISCO-BCOS/build/bin/`。详细编译步骤可参考：[编译](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/manual/get_executable.html#id5)。

## 帮助

```markdown
Usage:
      -h [ --help ]              help of rocksdb reader
      -c [ --createTable ] arg   [TableName] [KeyField] [ValueField]
      -p [ --path ] arg (=data/) [RocksDB path]
      -s [ --select ] arg        [TableName] [priKey]
      -u [ --update ] arg        [TableName] [priKey] [keyEQ] [valueEQ] [Key] 
                                 [NewValue]
      -i [ --insert ] arg        [TableName] [priKey] [Key]:[Value],...,[Key]:[Valu
                                 e]
      -r [ --remove ] arg        [TableName] [priKey]
e.g
	./rocksdb-storage -p ./nodes/127.0.0.1/node0/data/group1/block/RocksDB -s _sys_consensus_ node
```

## 选项介绍

### `-c`选项[Optional]

同 `--createTable` 选项，用于创建数据库表。参数包括：

- TableName：表名；
- KeyField：主键字段名；
- ValueField：其余字段名。如果有多个字段名则以符号 `,` 进行分隔。

### `-p`选项

同 `--path` 选项，用于指定节点 rocksdb 数据文件所在存储目录。rocksdb 默认文件路径为 `./node*/data/group*/block/RocksDB/`。

### `-s`选项[Optional]

同 `--select` 选项，用于查询数据库表中数据项记录。参数包括：

- TableName：表名；
- priKey：主键字段值；

> 补充：priKey 是 KeyFiled 的具体值。比如 _sys_consensus_ 系统表的主键是 name，主键值为 node。详细请参考：[AMDB](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/design/storage/storage.html)。

### `-u`选项[Optional]

同 `--update` 选项，用于更新数据库表中数据项记录。参数包括：

- TableName：表名；
- priKey：主键字段值；
- keyEQ：key 值，用于指定非主键字段名；
- valueEQ：value 值，用于指定非主键字段值，和 keyEQ 组合使用；
- Key：key 值，待修改的字段名；
- NewValue：value 值，待修改值。

> 补充：keyEQ 和 valueEQ 组合在一起的作用可以类比为 mysql 中的 where 条件。

### `-i`选项[Optional]

同 `--insert` 选项，用于新增数据库表中数据项记录。参数包括：

- TableName：表名；
- priKey：主键字段值，同时也是待插入的数据；
- `Key:Value`：以符号 `,` 连接的多个待插入数据。

### `-r`选项[Optional]

同 `--remove` 选项，用于删除数据库表中数据项记录。参数包括：

- TableName：表名；
- priKey：主键字段值，此处用于指定待删除的数据项

## 使用举例

1. 搭建单群组四节点区块链网络，可参考：[安装](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/installation.html)。搭建完之后需要将所有节点关闭，rocksdb-storage 工具只支持在节点关闭状态下使用

2. 编译 FISCO BCOS 源码，获取 rocksdb-storage 工具，移动工具至 `~/fisco/` 文件夹目录

3. **查询数据库表中数据项记录**：使用 `-s` 参数查看 `_sys_consensus_` 系统表中的节点数据。关于该系统表的说明可参考：[AMDB](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/design/storage/storage.html)

   ```shell
   # -p 指定 rocksdb 文件夹，-s 指定 TableName 和 priKey
   > ./rocksdb-storage -p ./nodes/127.0.0.1/node0/data/group1/block/RocksDB -s _sys_consensus_ node
   
   DB path : ./nodes/127.0.0.1/node0/data/group1/block/RocksDB
   select [_sys_consensus_,node] || params num : 2
   ================ open Table [_sys_consensus_] success! key node
   ***0 [ enable_num=0 ][ name=node ][ node_id=5ea630a8cad6681c9e54a30009caf2afd3fe75a01a46375a5c9ad947d42b3bae8830cd9dbf67ea5d3cccb3eb02cca9a0d91a65e56c97e3005cb85455b52fa89b ][ type=sealer ]
   ***1 [ enable_num=0 ][ name=node ][ node_id=6a6bfde992a8bc7b5fa77a5dfc5512df588b7ea8229ae48925929f77762f40e9bdecef420f5af7b752d9335f74cea270f3dd3a306db647e205dc8255acaefe32 ][ type=sealer ]
   ***2 [ enable_num=0 ][ name=node ][ node_id=6b5612f7206ef9559f1e33efc7f427bc4ab2e3a4674be4c7939f2ea49cb23dab341ab8f6f4b1586b38822e516b6a8391226bea2036df42fe295bf973b2a15ceb ][ type=sealer ]
   ***3 [ enable_num=0 ][ name=node ][ node_id=8e30de9cf192ee644b735d8a4765c7160a3227fa398e040eafa52cd834fd04260b5b960a6dc72c1a3ed8cbb713e29a875bdf8fb0b69f1e46c389068b551e90c7 ][ type=sealer ]
   ```

4. **更新数据库表中数据项记录**：使用 `-u` 参数修改 `_sys_consensus_` 系统表中的节点数据

   ```shell
   # -p 指定 rocksdb 文件夹，-u 指定 TableName、priKey、keyEQ、valueEQ、Key 和 NewValue
   > ./rocksdb-storage -p ./nodes/127.0.0.1/node0/data/group1/block/RocksDB -u _sys_consensus_ node node_id 5ea630a8cad6681c9e54a30009caf2afd3fe75a01a46375a5c9ad947d42b3bae8830cd9dbf67ea5d3cccb3eb02cca9a0d91a65e56c97e3005cb85455b52fa89b type observer
   
   DB path : ./nodes/127.0.0.1/node0/data/group1/block/RocksDB
   update [_sys_consensus_,node,node_id,5ea630a8cad6681c9e54a30009caf2afd3fe75a01a46375a5c9ad947d42b3bae8830cd9dbf67ea5d3cccb3eb02cca9a0d91a65e56c97e3005cb85455b52fa89b,type,observer] || params num : 6
   open Table [_sys_consensus_] success!
   condition is [node_id=5ea630a8cad6681c9e54a30009caf2afd3fe75a01a46375a5c9ad947d42b3bae8830cd9dbf67ea5d3cccb3eb02cca9a0d91a65e56c97e3005cb85455b52fa89b]
   update [type:observer]
   
   # 查看修改后的 _sys_consensus_ 系统表
   > ./rocksdb-storage -p ./nodes/127.0.0.1/node0/data/group1/block/RocksDB -s _sys_consensus_ node
   
   DB path : ./nodes/127.0.0.1/node0/data/group1/block/RocksDB
   select [_sys_consensus_,node] || params num : 2
   ================ open Table [_sys_consensus_] success! key node
   ***0 [ enable_num=0 ][ name=node ][ node_id=5ea630a8cad6681c9e54a30009caf2afd3fe75a01a46375a5c9ad947d42b3bae8830cd9dbf67ea5d3cccb3eb02cca9a0d91a65e56c97e3005cb85455b52fa89b ][ type=observer ]
   ***1 [ enable_num=0 ][ name=node ][ node_id=6a6bfde992a8bc7b5fa77a5dfc5512df588b7ea8229ae48925929f77762f40e9bdecef420f5af7b752d9335f74cea270f3dd3a306db647e205dc8255acaefe32 ][ type=sealer ]
   ***2 [ enable_num=0 ][ name=node ][ node_id=6b5612f7206ef9559f1e33efc7f427bc4ab2e3a4674be4c7939f2ea49cb23dab341ab8f6f4b1586b38822e516b6a8391226bea2036df42fe295bf973b2a15ceb ][ type=sealer ]
   ***3 [ enable_num=0 ][ name=node ][ node_id=8e30de9cf192ee644b735d8a4765c7160a3227fa398e040eafa52cd834fd04260b5b960a6dc72c1a3ed8cbb713e29a875bdf8fb0b69f1e46c389068b551e90c7 ][ type=sealer ]
   ```

5. **新增数据库表中数据项记录**：使用 `-i` 参数新增 `_sys_consensus_` 系统表中的节点数据

   ```shell
   # -p 指定 rocksdb 文件夹，-i 指定 TableName、priKey 和 [Key]:[Value],...,[Key]:[Value]，注意 priKey 为 peer
   > ./rocksdb-storage -p ./nodes/127.0.0.1/node0/data/group1/block/RocksDB -i _sys_consensus_ peer node_id:17f4ad3e71418be3e074c897c73fdd07a3a69a20524476dfbd4dc18fc6284714acf58d753bbc651549f7b5e30bfd82b84fdc5ac44e67aab8c1299ef92fd4e30e,type:sealer,enable_num:0
   
   DB path : ./nodes/127.0.0.1/node0/data/group1/block/RocksDB
   insert [_sys_consensus_,peer,node_id:17f4ad3e71418be3e074c897c73fdd07a3a69a20524476dfbd4dc18fc6284714acf58d753bbc651549f7b5e30bfd82b84fdc5ac44e67aab8c1299ef92fd4e30e,type:sealer,enable_num:0] || params num : 3
   open Table [_sys_consensus_] success!
   
   # 查看新增后的 _sys_consensus_ 系统表，注意 priKey 为 peer
   >  ./rocksdb-storage -p ./nodes/127.0.0.1/node0/data/group1/block/RocksDB -s _sys_consensus_ peer
   
   DB path : ./nodes/127.0.0.1/node0/data/group1/block/RocksDB
   select [_sys_consensus_,peer] || params num : 2
   ================ open Table [_sys_consensus_] success! key peer
   ***0 [ enable_num=0 ][ name=peer ][ node_id=17f4ad3e71418be3e074c897c73fdd07a3a69a20524476dfbd4dc18fc6284714acf58d753bbc651549f7b5e30bfd82b84fdc5ac44e67aab8c1299ef92fd4e30e ][ type=sealer ]
   ```

6. **删除数据库表中数据项记录**：使用 `-r` 参数删除 `_sys_consensus_` 系统表中的节点数据

   ```shell
   # -p 指定 rocksdb 文件夹，-r 指定 TableName 和 priKey，注意 priKey 为 peer
   > ./rocksdb-storage -p ./nodes/127.0.0.1/node0/data/group1/block/RocksDB -r _sys_consensus_ peer
   
   DB path : ./nodes/127.0.0.1/node0/data/group1/block/RocksDB
   remove [_sys_consensus_,peer] || params num : 2
   open Table [_sys_consensus_] success!
   
   # 查看删除后的 _sys_consensus_ 系统表，注意 priKey 为 peer
   > ./rocksdb-storage -p ./nodes/127.0.0.1/node0/data/group1/block/RocksDB -s _sys_consensus_ peer
   
   DB path : ./nodes/127.0.0.1/node0/data/group1/block/RocksDB
   select [_sys_consensus_,peer] || params num : 2
   ================ open Table [_sys_consensus_] success! key peer is empty!
   ```

7. **创建数据库表**：使用 `-c` 参数为数据库创建新的数据表 `user`，包括 username、age 和 sex 三个字段，其中 username 是主键

   ```shell
   # -p 指定 rocksdb 文件夹，-c 指定 TableName、KeyField 和 ValueField
   > ./rocksdb-storage -p ./nodes/127.0.0.1/node0/data/group1/block/RocksDB -c user username age,sex
   
   DB path : ./nodes/127.0.0.1/node0/data/group1/block/RocksDB
   createTable [user,username,age,sex] || params num : 3
   KeyField:[username]
   ValueField:[age,sex]
   createTable [user] success!
   
   # 插入数据
   > ./rocksdb-storage -p ./nodes/127.0.0.1/node0/data/group1/block/RocksDB -i user Bob age:20,sex:male
   
   DB path : ./nodes/127.0.0.1/node0/data/group1/block/RocksDB
   insert [user,Bob,age:20,sex:male] || params num : 3
   open Table [user] success!
   
   # 查看新插入的数据
   > ./rocksdb-storage -p ./nodes/127.0.0.1/node0/data/group1/block/RocksDB -s user Bob
   
   DB path : ./nodes/127.0.0.1/node0/data/group1/block/RocksDB
   select [user,Bob] || params num : 2
   ================ open Table [user] success! key Bob
   ***0 [ age=20 ][ sex=male ][ username=Bob ]
   ```

   