# mysql 常见指令

- shell 登录：`mysql -u root -p`

- 创建数据库：`create database name;`

- 查看已有数据库：`show databases;`

- 删除数据库：`drop database name;`

- 删除表：`drop table tableName;`

- 切换 database指令：`use databaseName;`

- 查看当前所在数据库信息：`status;` 和 `select database();`

- 查看当前所在数据库中数据表信息：`show tables;`

- 查看任意数据库中所有数据表指令（当前不在所查询数据库中也可）：`show tables from databaseName;`

- 查看当前数据库中数据表结构的语句：`show columns from tbale;`

---

## 删除所有表但是不删除数据库

执行如下指令：

```mysql
SELECT CONCAT('drop table ',table_name,';') FROM information_schema.`TABLES` WHERE table_schema='数据库名';
```

出现如下图的信息，手动执行每一条信息即可

![2](http://ww1.sinaimg.cn/large/006alGmrly1g2t27tb3iej30x809tteq.jpg)

**参考**：[Mysql删除所有表不删除数据库方法](https://blog.csdn.net/zenson_g/article/details/49205919)