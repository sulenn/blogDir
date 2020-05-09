# remote-ssh 插件

- 安装 remote-ssh 插件

- 配置服务器信息：

  ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gemhsvglo6j31820g6n09.jpg)

  按顺序点击

  ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gemhu9g697j30zw07baas.jpg)

  host为任意设置的服务器名；Hostname为ip地址；user为登录用户

- 配置好信息之后就会重新打开一个界面

  ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gemhwande6j30tm08kwfb.jpg)

  首先选择带连接的服务器类型，然后输入密码，之后就连接上了

- 为服务器设置本地 ssh 密钥，再次连接时免输密码。类似在 github 账号中添加密钥，之后用 ssh 上传代码时免输密码

  ​	![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gemhyyou1vj30ij055q35.jpg)

  输入 `ssh-keygen`;输入关键信息

  ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gemhzosoaij30t50bhtac.jpg)

  利用 `xshell` 的 `rz` 命令将 `id.rsa.pub` 文件传至服务器中，路径为 `/root/.ssh`

  ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gemi2r08lpj30ji0e2my3.jpg)

  在 `xshell` 中使用 vim 工具 `vim /etc/ssh/sshd_config` 查看以上两行代码是否被注释（不能被注释）

  进入路径 `/root/.ssh` 中执行命令：`cat id_rsa.pub >> authorized_keys`

  然后使用命令 `systemctl restart sshd` 重启 sshd 服务，之后再次连接就不要输入密码了

**参考**：



- [玩转VSCode插件之Remote-SSH](https://www.cnblogs.com/liyufeia/p/11405779.html)