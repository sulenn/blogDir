# mysql安装

## 1. ubuntu

## 1.1 通过 apt 安装

```shell
#命令1
sudo apt-get update
#命令2
sudo apt-get install mysql-server
sudo apt-get install mysql-client
```

![2](https://img-blog.csdn.net/2018062311511088)

**查看是否安装成功**：`mysql --version`

**配置 `MySQL`**：`sudo mysql_secure_installation` （可参考下面链接中的内容）

**参考**：[Ubuntu18.04 安装MySQL](https://blog.csdn.net/weixx3/article/details/80782479)

## 1.1.1 可能出现的错误

==注意错误：ERROR 1045 (28000): Access denied for user 'qiubing'@'localhost' (using password: NO)==

![2](http://ww1.sinaimg.cn/large/006alGmrly1g276wmdle6j30h201mweo.jpg)

**原因**:是因为在最近的Ubuntu安装（当然也可能是其他安装）中，MySQL默认使用了UNIX `auth_socket` plugin插件。

![2](http://ww1.sinaimg.cn/large/006alGmrly1g276y3do7sj30bw05j0sv.jpg)

**解决方案**:

先通过 `sudo mysql` ,进入同 `mysql` 进行交互的界面

然后执行如下指令：

```mysql
mysql> USE mysql;
mysql> UPDATE user SET plugin='mysql_native_password' WHERE User='root';
mysql> FLUSH PRIVILEGES;
mysql> exit;
```

**参考**：[解决 MySQL 的 ERROR 1698 (28000): Access denied for user 'root'@'localhost'](https://blog.csdn.net/jlu16/article/details/82809937)
