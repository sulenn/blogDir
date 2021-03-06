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

## 1.2 干净卸载

```shell
sudo rm /var/lib/mysql/ -R
sudo rm /etc/mysql/ -R
sudo apt-get autoremove --purge mysql-server
sudo apt-get remove mysql-common
sudo apt-get remove apparmor
dpkg -l |grep ^rc |awk '{print $2}' |sudo xargs dpkg -P
```

## 1.3 通过 dep 包安装

- 下载：下载地址为：[https://dev.mysql.com/downloads/](https://dev.mysql.com/downloads/)

  ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gennee8d5cj30zq0nsad4.jpg)

- 将 *MySQL APT repository* 添加至系统的软件仓库列表里：sudo dpkg -i mysql-apt-config_0.8.15-1_all.deb

- 安装：

  ```sql
  #命令1
  sudo apt-get update
  #命令2
  sudo apt-get install mysql-server
  ```

  *命令2安装，在界面中输入密码的下一步可能会卡主（无法点击确认键），可以尝试点击键盘 esc、f1、f2 键*

  *MySQL8.0采用了新的加密方式，与Ubuntu18.04有兼容问题；故选择下面的旧版本5.x的加密方式*

  ![image-20201016101536168](https://i.loli.net/2020/10/16/mv9lYsMuWzio5pJ.png)

- 配置 mysql 信息，参考链接

**参考**：[Ubuntu19.04 安装 MySQL 8.0.16](https://blog.csdn.net/weixx3/article/details/94133847)

---

## centos7 安装

**直接通过命令行的安装方式很慢，不知道为什么**

1. windows 下载 rpm 包。因为服务器下载贼慢

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gehfw42iu4j31400l5aee.jpg)

2. 检查是否已安装 mysql 和 mariadb

    检查mysql：`rpm -qa | grep mysql`
    卸载：`yum erase xxx`

    检查mariadb：`rpm -qa|grep -i mariadb`

3. 安装依赖包：`yum -y install make gcc-c++ cmake bison-devel ncurses-devel libaio libaio-devel`

4. 将已经下好的包上传到服务器上（=用xshell上传，花了两个半小时==），并解压

    解压：`tar xvf mysql-8.0.16-2.el7.x86_64.rpm-bundle.tar`

5. 安装common、libs、server、client 等四个包

    `rpm -ivh mysql-community-common-8.0.20-1.el7.x86_64.rpm`

    `rpm -ivh mysql-community-libs-8.0.20-1.el7.x86_64.rpm`

    `rpm -ivh mysql-community-client-8.0.20-1.el7.x86_64.rpm`

    `rpm -ivh mysql-community-server-8.0.20-1.el7.x86_64.rpm`

    安装 server 时可能会出错，可以使用 `yum -y install numactl` 解决

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gehll8xtgqj316p055dgv.jpg)

6. 启动 Mysql 服务：`service mysqld start`，查看临时密码用于登录 `grep "A temporary password" /var/log/mysqld.log`

7. 登录 `mysql -u root -p`，修改密码 `alter user user() identified by "...MYQ..."`，不能设置过于简单，除非修改 Mysql 密码设定策略。

**参考**：

- [centos7如何安装mysql](https://jingyan.baidu.com/article/08b6a59172b47e14a80922bc.html)
