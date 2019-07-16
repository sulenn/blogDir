# trustie 搭建区块链环境

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

- 检查 go 版本：`go version`

## 2. 更换 git 版本为 2.17.1

- 安装依赖包：

    ```shell
    yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel
    yum install  gcc perl-ExtUtils-MakeMaker
    ```

- 卸载旧的 git 版本：`yum remove git`

- 下载&解压：

    ```shell
    cd /usr/src
    wget https://mirrors.edge.kernel.org/pub/software/scm/git/git-2.17.1.tar.gz
    tar -zxvf git-2.17.1.tar.gz
    ```

- 编译安装：

    ```shell
    cd git-2.17.1
    make prefix=/usr/local/git all
    make prefix=/usr/local/git install
    echo "export PATH=$PATH:/usr/local/git/bin" >> /etc/bashrc
    source /etc/bashrc
    ```

- 检查git版本：`git --version`

    ==注意：如果安装完查看版本不是我们安装的最新版，请重新执行下面的操作==

    ```shell
    yum remove -y git
    source /etc/bashrc
    git --version
    ```

- 配置 git 全局信息：

    `git config --global user.password "..."`
    `git config --global user.name "..."`
    `git config user.email “your email id”`
    `git config --global credential.helper store`

**参考**：[如何在CentOS 6.x/7.x上安装git及最新版](https://my.oschina.net/antsky/blog/514586)

## 3. 下载并编译 geth，添加环境变量

- 下载 geth：`git clone http://git.trustie.net/Nigel/go-ethereum.git`

- 编译 geth：

    先进入 `go-ethereum` 目录，然后执行 `make all`即可

- 添加环境变量：

    `vim /etc/profile`
    然后在最后一行插入：

    `export PATH=$PATH:/home/pdl/ethereum/geth/go-ethereum/build/bin`

    运行如下命令，让修改在当前 bash 中生效，注意是当前，如果要一直生效需要重启电脑

    `source /etc/profile`

- 检查 geth 版本：`geth version`

## 4. 下载并编译 node，添加环境变量

- 删除原有的 nodejs（如果有的话）：`yum remove nodejs`

- 下载：`wget -O node-v10.16.0-linux-x64.tar.xz  https://npm.taobao.org/mirrors/node/v10.16.0/node-v10.16.0-linux-x64.tar.xz`

- 解压：

    将 `.tar.xz` 变为 `.tar`：`xz -d node-v10.16.0-linux-x64.tar.xz`

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

- 检查 node 版本：

    `node --version`
    `npm --version`

## 5. 下载 Chain_Creator_NodeJS

- 下载：`git clone http://git.trustie.net/Nigel/chain_creator_nodejs.git`

- 安装依赖：

    进入 `Chain_Creator_NodeJS` 目录，然后运行 `npm install --set-upstream`和`npm install -g --set-upstream`，可能会报一些错误，暂时先忽略

    可能会报错，看不懂报的错是啥意思。。不过好像不影响 node 程序的运行

    ![2](http://ww1.sinaimg.cn/large/006alGmrgy1g4z4yuqgaej31630rd183.jpg)
