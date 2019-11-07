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

- 删除某个表中所有数据信息：`truncate table table_name;`

- MySQL查询重复出现次数最多的记录：`SELECT keyword, count( * ) AS count  FROM article_keyword  GROUP BY keyword  ORDER BY count DESC  LIMIT 20`
    此段查询语句返回 article_keyword 表中 keyword 重复次数（count）最多的20条记录

---

## 删除所有表但是不删除数据库

执行如下指令：

```mysql
SELECT CONCAT('drop table ',table_name,';') FROM information_schema.`TABLES` WHERE table_schema='数据库名';
```

出现如下图的信息，手动执行每一条信息即可

![2](http://ww1.sinaimg.cn/large/006alGmrly1g2t27tb3iej30x809tteq.jpg)

**参考**：[Mysql删除所有表不删除数据库方法](https://blog.csdn.net/zenson_g/article/details/49205919)

---

## ubuntu 下 mysql 导入 excel 表

首先将表转为 `csv` 格式

然后终端，进入 mysql，进入对应的数据库，如 `HomeWork` 数据库，然后执行下面的指令

`load data local infile '/home/qiubing/桌面/paperTest.csv' into table paper fields terminated by ',' lines terminated by '\r\n';`

paper 是表名，与 csv 文件 /home/hrd/paper.csv 名称一致。

fields terminated by ‘,’表示数据之间用“，”隔开，这是csv文件的特性。lines terminated by ‘\r\n’表示每行数据以回车结束。

==注意：换行符可能为 '\n'==

**参考**：[将Excel表格或者CSV文件导入到Mysql中](http://www.linuxdiyf.com/linux/29149.html)

---

## ubuntu 下关闭、启动和重启 mysql 服务的方式

### 关闭

`service mysql stop`

### 启动

`service mysql start`

### 重启

`service mysql restart`

**参考**：[Linux平台下启动和关闭mysql服务](https://blog.csdn.net/king_1421484363/article/details/73277771)

---

## mysql 导入数据时出现--secure-file-priv 问题的解决办法

进行数据导入的时候出现 `--secure-file-priv option so it cannot execute this statement`

修改 `/etc/mysql/mysql.conf.d/mysqld.cnf` 文件

在末尾添加一句 `secure_file_priv="/"`即可将数据导出到任意目录

**参考**：[mysql导出数据时出现--secure-file-priv 问题的解决办法](https://blog.csdn.net/qq473179304/article/details/55107166)

---
