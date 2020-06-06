# electron-ssr

==shadowsocks-qt5 不好用==

==github 上 electron 项目非常危险，原始项目已经被封了。所以我会保存一份软件在网盘中 `研究生/开发/ubuntu/electron-ssr`==

## 下载

**下载地址**：[https://github.com/qingshuisiyuan/electron-ssr-backup/releases](https://github.com/qingshuisiyuan/electron-ssr-backup/releases)

![image.png](http://ww1.sinaimg.cn/large/006alGmrgy1g84k07wfjzj31150mzjud.jpg)

## 安装教程

**安装依赖**:

`sudo apt install libcanberra-gtk-module libcanberra-gtk3-module gconf2 gconf-service libappindicator1`

**注意要安装python2.7，新装的 ubuntu 18.04 可能不带有python2.7。上一次就是这个问题导致调试了很长时间**

**安装软件**:

`sudo dpkg -i *.deb`

**运行软件**:

终端输入 `electron-ssr`

**添加订阅**:

**注意手动敲可能会出错，可以用微信网页复制粘贴**

![image.png](http://ww1.sinaimg.cn/large/006alGmrgy1g84k5d0k57j30ow0fmmyz.jpg)

**选择服务节点**：

![image.png](http://ww1.sinaimg.cn/large/006alGmrgy1g84k6mb0o5j30pa0fq77z.jpg)

![image.png](http://ww1.sinaimg.cn/large/006alGmrgy1gfipkz9yosj30jb0hzwgl.jpg)

**系统设置（这一步可设置也可不设置，如果不设置不能用的话就选自动代理，如果还是不行那就再设置）**:

系统设置-网络设置-网络代理设置为如下图所示

![image.png](http://ww1.sinaimg.cn/large/006alGmrgy1g84k3z6t3lj30o20hpq58.jpg)

**选择开机启动**：

![image.png](http://ww1.sinaimg.cn/large/006alGmrgy1g84k82mkp6j30s60i8mzz.jpg)

==至此就可以了，如果用不了就重启电脑==

**如果实在用不了就看日志，看看是啥问题。日志路径：/homa/Username/.config/electron-ssr/logs/shadowsocksr-client.log**

**如果终端翻不了墙的话，就在/etc/profile中配置一下代理。如下**：

![image.png](http://ww1.sinaimg.cn/large/006alGmrgy1gfiuyhzdflj30fl083dg4.jpg)

**如果终端 docker pull 翻不了墙的话，就在 docker 的文件中配置一下代理。文件可能位于/usr/lib/systemd/system/docker.servic，也可能位于 /usr/lib/systemd/system/docker.service。注意图中端口号**

**添加好之后需要重启docker 服务，systemctl daemon-reload 和 systemctl restart docker 两条命令。如果 docker 还是很慢的话，可以参考链接2**

![image.png](http://ww1.sinaimg.cn/large/006alGmrgy1gfj074x5xyj30xz0c2abz.jpg)

**参考**:

- [Debian系列——Ubuntu18.04为例](https://github.com/qingshuisiyuan/electron-ssr-backup/blob/master/Ubuntu.md)

- [Ubuntu和docker使用shadowsocks客户端代理](https://zhanghongtong.github.io/2019/06/27/Ubuntu%E5%92%8Cdocker%E4%BD%BF%E7%94%A8shadowsocks%E5%AE%A2%E6%88%B7%E7%AB%AF%E7%BF%BB%E5%A2%99/)