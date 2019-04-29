# nodejs 和 npm 安装

##　1. 安装Node.js

Node 官网已经把 linux 下载版本更改为已编译好的版本了，我们可以直接下载解压后使用：

官网下载最新版本：[http://nodejs.cn/download/](http://nodejs.cn/download/)

```shell
tar xf  node-v10.9.0-linux-x64.tar.xz       // 解压
cd node-v10.9.0-linux-x64/                  // 进入解压目录
./bin/node -v                               // 执行node命令 查看版本
```

解压文件的 bin 目录底下包含了 node、npm 等命令，我们可以使用 ln 命令来设置软连接：

```shell
ln -s /usr/software/nodejs/bin/npm   /usr/local/bin/
ln -s /usr/software/nodejs/bin/node   /usr/local/bin/
```

==注意：通过这个方法安装之后，每次 `npm install -g solc` 全局安装的包都需要手动设置软连接，方法同上==

==注意：实验室电脑，node 安装的地址为 `/home/qiubing/node-v10.15.3-linux-x64/`，于是每次全局下载的包其对应的 `bin` 位于 `/home/qiubing/node-v10.15.3-linux-x64/lib/node_modules/`==

![2](http://ww1.sinaimg.cn/large/006alGmrly1g2jq6ewtiuj30m70dnaj8.jpg)

## 2. 使用淘宝 NPM 镜像

大家都知道国内直接使用 npm 的官方镜像是非常慢的，这里推荐使用淘宝 NPM 镜像。

淘宝 NPM 镜像是一个完整 npmjs.org 镜像，你可以用此代替官方版本(只读)，同步频率目前为 10分钟 一次以保证尽量与官方服务同步。

你可以使用淘宝定制的 cnpm (gzip 压缩支持) 命令行工具代替默认的 npm:

```shell
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

这样就可以使用 cnpm 命令来安装模块了：

```shell
cnpm install [name]
```

**参考**：[http://www.runoob.com/nodejs/nodejs-install-setup.html](http://www.runoob.com/nodejs/nodejs-install-setup.html)