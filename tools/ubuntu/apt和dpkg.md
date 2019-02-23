# apt 和 dpkg

## apt-get 和 dpkg 区别

两者的区别是dpkg绕过apt包管理数据库对软件包进行操作，所以你用dpkg安装过的软件包用apt可以再安装一遍，系统不知道之前安装过了，将会覆盖之前dpkg的安装。
1、dpkg是用来安装.deb文件,但不会解决模块的依赖关系,且不会关心ubuntu的软件仓库内的软件,可以用于安装本地的deb文件。
2、apt会解决和安装模块的依赖问题,并会咨询软件仓库, 但不会安装本地的deb文件, apt是建立在dpkg之上的软件管理工具。

### 安装软件包

```shell
dpkg -i package_name.deb      //安装本地软件包，不解决依赖关系
apt-get install package                    //在线安装软件包
apt-get install package --reinstall   //重新安装软件包
```

### 移除软件包

```shell
dpkg -r package                               //删除软件包
apt-get remove package                  //同上
dpkg -P                              //删除软件包及配置文件
apt-get remove package --purge    //删除软件包及配置文件
```

## 查看已安装包

查看所有已安装包：`dpkg -l`

查看某个已安装包：`dpkg -l packageName`

查看某个已安装包详细：`dpkg -s packageName`

参考：

- [Ubuntu 16.04系统下apt-get和dpkg区别](https://www.cnblogs.com/wenzheshen/p/6558493.html)

- [Ubuntu的apt-get命令与dpkg命令](https://blog.csdn.net/ycq521131/article/details/80150673)