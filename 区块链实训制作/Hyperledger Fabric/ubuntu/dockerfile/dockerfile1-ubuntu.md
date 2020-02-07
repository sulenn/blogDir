# dockerfile1

==最终的 dockerfile==

```dockerfile
    FROM ubuntu:18.04
# 更换源并下载基础软件工具
    ADD ./sources.list /etc/apt/sources.list
    RUN apt-get update; apt-get -y install git curl make gcc wget vim ; rm -rf /var/lib/apt/lists/*
# 安装 docker
    RUN apt-get update; apt-get -y install apt-transport-https software-properties-common;rm -rf /var/lib/apt/lists/*
    RUN curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add - \
    && add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    RUN apt-get update; apt-get -y install docker-ce=18.06.3~ce~3-0~ubuntu ;rm -rf /var/lib/apt/lists/*
# 安装 docker-compose
    RUN apt-get update; apt-get -y install python-pip ;rm -rf /var/lib/apt/lists/*
    RUN pip install --upgrade pip -i https://pypi.tuna.tsinghua.edu.cn/simple\
    && pip install docker-compose -i https://pypi.tuna.tsinghua.edu.cn/simple
# 下载 golang 和 nodejs
    WORKDIR /opt
    COPY ./go1.11.1.linux-amd64.tar.gz /opt
    COPY ./node-v8.16.0-linux-x64.tar.xz /opt
    RUN mkdir -p go
    ENV GOPATH=/opt/go
# 下载 fabric 和 fabric-samples
    ADD ./fabric $GOPATH/src/github.com/hyperledger/fabric

# 下载 hyperledger fabric 镜像
    ADD ./hyperledger-fabric-images /opt/hyperledger-fabric-images
```

用于 Hyperledger Fabric 基础环境搭建的 dockerfile 文件记录

## 1. 除 golang 和 Nodejs 的 dockerfile 搭建

build 后的镜像大小为：793M

```dockerfile
    FROM ubuntu:18.04
    RUN apt-get update
    RUN apt-get -y install git
    RUN apt-get -y install curl
    RUN apt-get -y install make
    RUN apt-get -y install gcc
    RUN apt-get -y install wget
    RUN apt-get -y install vim
# 安装 docker
    RUN apt-get -y install apt-transport-https
    RUN apt-get -y install software-properties-common
    RUN curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
    RUN add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    RUN apt-get update
    RUN apt-get -y install docker-ce=18.06.3~ce~3-0~ubuntu
# 启动 docker
    # RUN service docker start
# 安装 docker-compose
    RUN apt-get -y install python-pip
    RUN pip install --upgrade pip
    RUN pip install docker-compose
```

---

## 2. 包括 golang 和 nodejs 的基础环境安装

build 后的镜像大小为：932MB

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbjlfka985j30zn08ct9x.jpg)

```dockerfile
    FROM ubuntu:18.04
    RUN apt-get update
    RUN apt-get -y install git
    RUN apt-get -y install curl
    RUN apt-get -y install make
    RUN apt-get -y install gcc
    RUN apt-get -y install wget
    RUN apt-get -y install vim
# 安装 docker
    RUN apt-get -y install apt-transport-https
    RUN apt-get -y install software-properties-common
    RUN curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
    RUN add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    RUN apt-get update
    RUN apt-get -y install docker-ce=18.06.3~ce~3-0~ubuntu
# 启动 docker
    # RUN service docker start
# 安装 docker-compose
    RUN apt-get -y install python-pip
    RUN pip install --upgrade pip
    RUN pip install docker-compose
# 下载安装 golang 和 nodejs
    WORKDIR /opt 
    RUN wget https://dl.google.com/go/go1.11.1.linux-amd64.tar.gz
    RUN wget https://nodejs.org/dist/v8.16.0/node-v8.16.0-linux-x64.tar.xz
```

## 最终交付的 dockerfile1

build 后的镜像大小为：2.47G

```dockerfile
    FROM ubuntu:18.04
    RUN apt-get update
    RUN apt-get -y install git
    RUN apt-get -y install curl
    RUN apt-get -y install make
    RUN apt-get -y install gcc
    RUN apt-get -y install wget
    RUN apt-get -y install vim
# 安装 docker
    RUN apt-get -y install apt-transport-https
    RUN apt-get -y install software-properties-common
    RUN curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
    RUN add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    RUN apt-get update
    RUN apt-get -y install docker-ce=18.06.3~ce~3-0~ubuntu
# 启动 docker，没有权限无法启动
    # RUN service docker start
# 安装 docker-compose
    RUN apt-get -y install python-pip
    RUN pip install --upgrade pip
    RUN pip install docker-compose
# 下载安装 golang 和 nodejs
    WORKDIR /opt 
    RUN wget https://dl.google.com/go/go1.11.1.linux-amd64.tar.gz
    RUN wget https://nodejs.org/dist/v8.16.0/node-v8.16.0-linux-x64.tar.xz
# 配置 GOPATH 路径
    RUN mkdir -p go
    ENV GOPATH=/opt/go
# 下载 fabric 源码
    RUN mkdir -p $GOPATH/src/github.com/hyperledger
    WORKDIR $GOPATH/src/github.com/hyperledger
    RUN git clone https://github.com/hyperledger/fabric.git
    WORKDIR $GOPATH/src/github.com/hyperledger/fabric
    RUN git checkout -b release-1.4
    WORKDIR $GOPATH/src/github.com/hyperledger/fabric/scripts
# 通过脚本下载镜像和二进制可执行文件这条命令需要开启docker才能下载
    # RUN ./bootstrap.sh 1.4.0
```
