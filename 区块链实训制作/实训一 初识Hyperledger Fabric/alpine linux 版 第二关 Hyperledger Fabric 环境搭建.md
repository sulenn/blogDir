[TOC]

---

####任务描述


本关任务：安装与配置 Hyperledger Fabric 基础环境。


####相关知识

Hyperledger Fabric 主版本是由 go 语言编写，提供 nodejs、java、go 等 rpc 接口调用，其基础环境搭建起来比较复杂。这里会对安装步骤进行简化，并提供在线的命令行窗口工具进行测试。该实训提供的命令行工具基于 `docker:19.03.5-dind` 镜像。

#####安装基础软件工具

执行如下命令安装基础软件

```shell
apk add --no-cache git curl make gcc wget vim
```

各软件的作用：

- `git`：用于下载 fabric 托管在 github 上的源码

- `curl`：网络工具，能够通过 http、ftp 等方式下载文件

- `make`：编译 makefile 脚本文件描述的整个工程的规则

- `gcc`：GNU 编译器套件，是 Linux 下默认的 C/C++ 编译器

- `wget`：下载工具

- `vim`：Linux 系统中的编辑工具

>提示：平台提供的命令行工具中，以上软件均已安装。

#####安装 docker

Docker 是一个开源的应用容器引擎，基于 Go 语言 并遵从 Apache2.0 协议开源。

Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。

`docker:19.03.5-dind` 镜像内嵌支持 docker，故无需重复安装。

已安装的 `docker` 版本信息 `docker version` 为 ：

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbsrk0bmb3j30nv0l740f.jpg)

#####安装 docker-compose

compose 是 docker 容器进行编排的工具，是定义和运行多容器的应用，可以一条命令启动多个容器。使用 docker-compose 后就不再需要使用 shell 脚本来启动容器。

1. 首先安装 `python-pip`

```shell
apk add --no-cache py2-pip
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

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbsrq2v7y0j30ht03zq34.jpg)
<br>

>提示：平台提供的命令行工具中已安装 docker-compose。

#####配置 golang 环境

Go 是一个开源的编程语言，它能让构造简单、可靠且高效的软件变得容易。以简洁、快速、安全、并行、有趣、开源、内存管理、数组安全、编译迅速等特色闻名。

1. 下载 `golang`，地址: [https://golang.google.cn/dl/](https://golang.google.cn/dl/)

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbn0dmc19qj31dr0x1wki.jpg)

    >平台已将软件包下载好，位于 /opt 目录下。

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
    # 创建符号链接修复 go 在linux 环境中的依赖项
	mkdir /lib64
	ln -s /lib/libc.musl-x86_64.so.1 /lib64/ld-linux-x86-64.so.2
    ```

4. 检查安装是否成功，运行

    ```shell
    go version
    ```

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbn0n1lin8j30ej02kjre.jpg)

#####配置 node 和 npm 环境

Node.js 是一个基于 Chrome JavaScript 运行时建立的一个平台。

Node.js 是一个事件驱动 I/O 服务端 JavaScript 环境，基于 Google 的 V8 引擎，V8 引擎执行 Javascript 的速度非常快，性能非常好。

npm 是 nodejs 的包管理工具，能解决 nodejs 代码部署上的很多问题，常见的使用场景有以下几种：

- 允许用户从NPM服务器下载别人编写的第三方包到本地使用。

- 允许用户从NPM服务器下载并安装别人编写的命令行程序到本地使用。

- 允许用户将自己编写的包或命令行程序上传到NPM服务器供别人使用。

1. 命令安装 `nodejs` 和 `npm`：

    ```shell
    apk add node npm
    ```

2. 检查安装是否成功，运行

    ```shell
    node --version
    npm --version
    ```

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbssms1d06j309j01mjr7.jpg)

<br>

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbssoeh87gj309d01mglf.jpg)

####编程要求

在右侧命令行中配置好 Hyperledger Fabric 基础环境后，点击评测即可。平台会检测 `go version` 和 `node version` 是否正确。

---
开始你的任务吧，祝你成功！