[TOC]

---

####任务描述


本关任务：安装与配置 Hyperledger Fabric 基础环境


####相关知识

Hyperledger Fabric 主版本是由 go 语言编写，提供 nodejs、java 等 rpc 接口调用，其基础环境搭建起来比较复杂。这里我们会对安装步骤进行简化，并提供在线的命令行窗口进行测试。如果学生想在本地安装运行的话，推荐使用64位Linux操作系统： ubuntu:18.04。

#####安装基础软件工具

执行如下命令安装基础软件

```shell
apt-get -y install git curl make gcc wget vim
```

各软件的作用：

- `git`：用于下载 fabric 托管在 github 上的源码

- `curl`：网络工具，能够通过http、ftp等方式下载文件

- `make`：编译 makefile 脚本文件描述的整个工程的规则

- `gcc`：GNU编译器套件，是Linux下默认的C/C++编译器

- `wget`：下载工具

- `vim`：linux 系统中的编辑工具

>提示：平台提供的命令行工具中这些软件均已安装

#####安装 docker

Docker 是一个开源的应用容器引擎，基于 Go 语言 并遵从 Apache2.0 协议开源。

Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。

1. 首先安装必要的两个工具：`apt-transport-https` 和 `software-properties-common`

    ```shell
    apt-get -y install apt-transport-https software-properties-common
    ```

2. 然后添加 docker 的 Debian 软件源

    ```shell
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -

    add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

3. 最后安装 `docker-ce`，注意版本为 `18.06.3`

    ```shell
    apt-get -y install docker-ce=18.06.3~ce~3-0~ubuntu
    ```

4. 检查安装是否成功，运行

    ```shell
    docker version
    ```

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbmzy2fwqtj3183080t9o.jpg)

>提示：平台提供的命令行工具中已安装 docker

#####安装 docker-compose

compose 是 docker 容器进行编排的工具，是定义和运行多容器的应用，可以一条命令启动多个容器。使用 docker-compose 后就不再需要使用 shell 脚本来启动容器。

1. 首先安装 `python-pip`

    ```shell
    apt-get -y install python-pip
    ```

2. 然后升级 `pip`

    ```shell
    pip install --upgrade pip -i https://pypi.tuna.tsinghua.edu.cn/simple
    ```

3. 最后使用 `pip` 安装 `docker-compose`

    ```shell
    pip install docker-compose -i https://pypi.tuna.tsinghua.edu.cn/simple
    ```

4. 检查安装是否成功，运行

    ```shell
    docker-compose version
    ```

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbn01nc8wlj31aw03u3yz.jpg)

>提示：平台提供的命令行工具中已安装 docker-compose

#####配置 golang 环境

Go 是一个开源的编程语言，它能让构造简单、可靠且高效的软件变得容易。以简洁、快速、安全、并行、有趣、开源、内存管理、数组安全、编译迅速等特色闻名。

1. 下载 `golang`，地址: [https://golang.google.cn/dl/](https://golang.google.cn/dl/)

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbn0dmc19qj31dr0x1wki.jpg)

    >平台已将软件包下载好，位于 /opt 目录下

2. 解压软件包

    ```shell
    # 切换目录
    cd /opt
    # 解压软件包
    tar -C /usr/local -xzf go1.11.1.linux-amd64.tar.gz
    ```

3. 添加环境变量

    ```shell
    # 添加环境变量
    echo "export PATH=$PATH:/usr/local/go/bin" >> /etc/profile
    # 激活
    source /etc/profile
    ```

4. 检查安装是否成功，运行

    ```shell
    go version
    ```

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbn0n1lin8j30ej02kjre.jpg)

#####配置 node（npm） 环境

Node.js 是一个基于Chrome JavaScript 运行时建立的一个平台。

Node.js 是一个事件驱动I/O服务端JavaScript环境，基于Google的V8引擎，V8引擎执行Javascript的速度非常快，性能非常好。

1. 下载 `nodejs`，地址: [https://nodejs.org/dist/v8.16.0/](https://nodejs.org/dist/v8.16.0/)

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbn0ynlzt6j30yr0wlagz.jpg)

    >平台已将软件包下载好，位于 /opt 目录下

2. 解压软件包

    ```shell
    # 解压软件包
    xz -d node-v8.16.0-linux-x64.tar.xz
    tar xvf node-v8.16.0-linux-x64.tar
    # 移动文件夹
    mv node-v8.16.0-linux-x64 /usr/local/node
    ```

3. 添加环境变量

    ```shell
    # 添加环境变量
    echo "export PATH=/usr/local/node/bin:$PATH" >> /etc/profile
    # 激活
    source /etc/profile
    ```

4. 检查安装是否成功，运行

    ```shell
    node --version
    ```

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbn0sqtozej30fs02k3yh.jpg)

####编程要求

在右侧命令行中配置好 Hyperledger Fabric 基础环境后点击评测即可。

---
开始你的任务吧，祝你成功！