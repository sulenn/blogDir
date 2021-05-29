# shadowsocks-qt5

## 1. 安装shadowsocks-qt5

==注意：可跳过 1.1 和 1.2==

直接用 `Shadowsocks-Qt5-3.0.1-x86_64` 即可

然后赋予其执行权限

![2](http://ww1.sinaimg.cn/large/006alGmrgy1g0wxm3wf9uj30n80fjdhe.jpg)

### 1.1 ubuntu16.04

```shell
sudo add-apt-repository ppa:hzwhuang/ss-qt5
sudo apt-get update
sudo apt-get install shadowsocks-qt5
```

第一行代码是添加软件的ppa源，当然，这是作者提供的

然后执行update更新并安装shadowsocks-qt5。

如果安装成功之后，按win键搜索应该能够找到软件，如下图所示:

![2](http://ww1.sinaimg.cn/large/006alGmrly1fzinc5bfsnj305a06bwf9.jpg)

### 1.2 ubuntu18.04

参考：[Ubuntu18.04 安装ss-qt5 缺少依赖文件](https://blog.csdn.net/a912952381/article/details/81172971)

#### 1.2.1 添加 ppa 源

```shell
sudo add-apt-repository ppa:hzwhuang/ss-qt5
```

但是 ppa:hzwhuang/ss-qt5 并没有18.04的源，所以会报错：

```shell
E: 仓库 “http://ppa.launchpad.net/hzwhuang/ss-qt5/ubuntu bionic Release”
```

修改其版本代号即可：

```shell
cd /etc/apt/sources.list.d/
sudo vim hzwhuang-ubuntu-ss-qt5-bionic.list
```

文件内容修改为：（ bionic 是18.04版本代号， xenial 是16.04的版本代号）

```shell
deb http://ppa.launchpad.net/hzwhuang/ss-qt5/ubuntu xenial main
# deb-src http://ppa.launchpad.net/hzwhuang/ss-qt5/ubuntu bionic main
```

#### 1.2.2 更新并安装 ssqt

执行：

```shell
sudo apt-get update
sudo apt-get install shadowsocks-qt5 
```

如果安装成功之后，按win键搜索应该能够找到软件，如下图所示:

![2](http://ww1.sinaimg.cn/large/006alGmrly1fzinc5bfsnj305a06bwf9.jpg)

## 2. 配置shadowsocks-qt5

如图

![2](http://ww1.sinaimg.cn/large/006alGmrly1fzincefungj30ry0fdn1m.jpg)

此外也可以手动配置

## 3. 配置系统代理(这一步可以舍弃)

==如果加上这一步会出现网易云音乐等APP无法使用的情况==

单独配置shadowsocks-qt5并不能使用网络，所以还要配置系统的代理。

打开 `设置-网络-网络代理`

代理方式选择 `手动` ，第一个 `http代理` 清空配置，端口设置为 `0` ，然后第四个 `socks代理` 设置为 `127.0.0.1` ，端口填写 `1080` ，ip和端口在`shadowsocks-qt5` 中有设置，使用默认的就好

##　4. 配置浏览器（Chrome）

应该来说现在能上网了，但是对于浏览器来说，我们可以添加插件用来自动判断当前网址是否需要科学上网。

我在这里使用的是chrome，所以使用chrome的方法来写教程。

### 一. 安装插件

我们需要给chrome安装SwitchyOmega插件，但是没有代理之前是不能从谷歌商店安装这个插件的，但是我们可以从Github上直接下载最新版 [https://github.com/FelisCatus/SwitchyOmega/releases/](https://github.com/FelisCatus/SwitchyOmega/releases/)（这个是chrome的）然后浏览器地址打开chrome://extensions/，将下载的插件托进去安装。

==这里可能会出现 "无法从该网站添加应用，拓展程序或脚本" 问题，可以参考 ubuntu 目录下 "问题解决" 文件==

### 二. 设置代理地址

安装好插件会自动跳到设置选项，有提示你可以跳过。左边新建情景模式-选择代理服务器-比如命名为SS（叫什么无所谓）其他默认之后创建，之后在代理协议选择SOCKS5，地址为127.0.0.1,端口默认1080 。然后保存即应用选项。

![2](http://ww1.sinaimg.cn/large/006alGmrly1fzincu11s2j30gm0h4wgg.jpg)

![2](http://ww1.sinaimg.cn/large/006alGmrly1fzind2q63xj30w80eugnf.jpg)

### 三. 设置自动切换

接着点击自动切换 ( Auto switch）上面的不用管，在按照规则列表匹配请求后面选择刚才新建的SS，默认情景模式选择直接连接。点击应用选项保存。再往下规则列表设置选择AutoProxy 然后将这个 [地址](https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt) 填进去，点击下面的立即更新情景模式，会有提示更新成功！

![2](http://ww1.sinaimg.cn/large/006alGmrly1fzindbc2f3j31050iatbk.jpg)

点击浏览器右上角的SwitchyOmega图标，下面选择自动切换，然后打开google.com试试

## 5. 添加shadowsocks-qt5自动启动

使用一段时间后你应该会发现每次开机都要手动运行shadowsocks-qt5，所以我们还要添加shadwsocks-qt5的自动启动。

`Ctrl+Alt+T` 打开终端，输入下面的指令：

`gnome-session-properties`

然后弹出这样的窗口：

![2](http://ww1.sinaimg.cn/large/006alGmrly1fzindz385zj30ge0edwfa.jpg)

点击右边的add，然后选择/usr/bin/ss-qt5，名称随便输，保存即可。

## 6. 总结

**参考至**：

[https://mystery0.vip/2017/01/12/Ubuntu%E4%BD%BF%E7%94%A8Shadowsocks-qt5%E7%A7%91%E5%AD%A6%E4%B8%8A%E7%BD%91/](https://mystery0.vip/2017/01/12/Ubuntu%E4%BD%BF%E7%94%A8Shadowsocks-qt5%E7%A7%91%E5%AD%A6%E4%B8%8A%E7%BD%91/)

[https://www.sundabao.com/ubuntu%E4%BD%BF%E7%94%A8shadowsocks/](https://www.sundabao.com/ubuntu%E4%BD%BF%E7%94%A8shadowsocks/)