# trustie 搭建 geth 环境

在 `/home/pdl` 目录下新建 `/ethereum/geth` 文件夹，然后在该文件夹中进行如下操作

## 1. 安装 go 环境

- 下载 go：`wget -O go1.12.7.linux-amd64.tar.gz  https://dl.google.com/go/go1.12.7.linux-amd64.tar.gz`

- 解压：`tar -C /usr/local -xzf go1.12.7.linux-amd64.tar.gz`

- 永久添加环境变量：

    `vim /etc/profile`

    然后在最后一行插入：

    `export PATH=$PATH:/usr/local/go/bin`

    运行如下命令，让修改在当前 bash 中生效，注意是当前，如果要一直生效需要重启电脑

    `source /etc/profile`

## 2. 下载并编译 geth，添加环境变量

- 下载 geth：`git clone http://git.trustie.net/Nigel/go-ethereum.git`

- 编译 geth：

    先进入 `go-ethereum` 目录，然后执行 `make all`即可

- 添加环境变量：

    `vim /etc/profile`
    然后在最后一行插入：

    `export PATH=$PATH:/home/pdl/ethereum/geth/go-ethereum/build/bin`

    运行如下命令，让修改在当前 bash 中生效，注意是当前，如果要一直生效需要重启电脑

    `source /etc/profile`

## 3. 下载并编译 node，添加环境变量

- 删除原有的 nodejs：`yum remove nodejs`

- 下载：`wget -O node-v10.16.0-linux-x64.tar.xz  https://npm.taobao.org/mirrors/node/v10.16.0/node-v10.16.0-linux-x64.tar.xz`

- 解压：

    将 `.tar.xz` 变为 .tar：`xz -d node-v10.16.0-linux-x64.tar.xz`

    解压 `.tar`：`tar -C /usr/local -xvf node-v10.16.0-linux-x64.tar`

- 修改文件名：

    `cd /usr/local`

    `mv node-v10.16.0-linux-x64/ node`

- 永久添加环境变量：

    `vim /etc/profile`

    然后在最后一行插入：

    `export PATH=$PATH:/usr/local/node/bin`

    运行如下命令，让修改在当前 bash 中生效，注意是当前，如果要一直生效需要重启电脑

    `source /etc/profile`

## 4. 下载 Chain_Creator_NodeJS

- 下载：`git clone http://git.trustie.net/Nigel/chain_creator_nodejs.git`

- 安装依赖：

    进入 `Chain_Creator_NodeJS` 目录，然后运行 `npm install`和`npm install -g`，可能需要多运行几次。。。

    可能会报错，看不懂报的错是啥意思。。不过好像不影响 node 程序的运行

    ![2](http://ww1.sinaimg.cn/large/006alGmrgy1g4z4yuqgaej31630rd183.jpg)

==测试 node 功能的时候需要配置 git 的全局信息，如 user.name、user.password、user.email 啥的==
