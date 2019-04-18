# mysql 常见指令

- shell 登录：`mysql -u root -p`

- 创建数据库：`create database name;`

- 查看已有数据库：`show databases;`

- 删除数据库：`drop database name;`

- 切换 database指令：`use databaseName;`

- 查看当前所在数据库信息：`status;` 和 `select database();`

- 查看当前所在数据库中数据表信息：`show tables;`

- 查看任意数据库中所有数据表指令（当前不在所查询数据库中也可）：`show tables from databaseName;`