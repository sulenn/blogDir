# dockerfile1

用于 Hyperledger Fabric 基础环境搭建的 dockerfile 文件记录

## 1. 除 golang 和 Nodejs 的 dockerfile 搭建

### 只有一个 RUN 命令

build 后的镜像大小为：839M

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbjkrvgrp4j30x00d3dhv.jpg)

```dockerfile
FROM centos:7.6.1810
# yun install 基础环境
RUN yum -y install git \
    curl \
	ca-certificates \
	make \
	gcc \
	wget \
	yum-utils \
    && yum update -y \
# 安装 docker-ce
    && yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo \
    && yum makecache \
    && yum -y install docker-ce-18.06.3.ce \
# 启动 docker ce，暂时先不启动 docker，会报错：Failed to get D-Bus connection: Operation not permitted
#    && systemctl start docker \
#    && systemctl enable docker
# 安装 docker-compose
# 使用注释的两条命令会出现错误：error: open of failed: No such file or directory。可能是 rpm 后面的空格识别问题
# ​	 && rpm -ivh http://dl.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm \
# ​    && yum clean all \
    && rpm -ivh http://dl.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm  && yum clean all \
    && yum -y remove epel-release \
    && yum -y install epel-release --enablerepo=extras \
    && yum -y install python-pip \
    && pip install --upgrade pip \
    && yum -y install python-devel \
    && pip install docker-compose
```

### 全 RUN 命令

build 后的镜像大小为：2.33G

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbjkqi6wu9j310r0gbgo8.jpg)

```dockerfile
FROM centos:7.6.1810
# yun install 基础环境
    RUN yum -y install git
    RUN yum -y install curl
    RUN yum -y install	ca-certificates
    RUN yum -y install	make
    RUN yum -y install	gcc
    RUN yum -y install	wget
    RUN yum -y install	yum-utils
    RUN yum update -y

    # 安装 docker-ce
    RUN yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
    RUN yum makecache
    RUN yum -y install docker-ce-18.06.3.ce
    # 启动 docker ce，暂时先不启动 docker，会报错：Failed to get D-Bus connection: Operation not permitted
    #    && systemctl start docker \
    #    && systemctl enable docker
    # 安装 docker-compose
    # 使用注释的两条命令会出现错误：error: open of failed: No such file or directory。可能是 rpm 后面的空格识别问题
    # ​	 && rpm -ivh http://dl.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm \
    # ​    && yum clean all \
    RUN rpm -ivh http://dl.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm  && yum clean all
    RUN yum -y remove epel-release
    RUN yum -y install epel-release --enablerepo=extras
    RUN yum -y install python-pip
    RUN pip install --upgrade pip
    RUN yum -y install python-devel
    RUN pip install docker-compose
```

---

## 2. 包括 golang 和 nodejs 的基础环境安装

### 只有一个 RUN 命令

build 后的镜像大小为：1.42G

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbk1zwvpo0j317g0cgtar.jpg)

```dockerfile
    FROM centos:7.6.1810
# yun install 基础环境
    RUN yum -y install git \
        curl \
        ca-certificates \
        make \
        gcc \
        wget \
        yum-utils \
        && yum update -y \
    # 安装 docker-ce
        && yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo \
        && yum makecache \
        && yum -y install docker-ce-18.06.3.ce \
    # 启动 docker ce，暂时先不启动 docker，会报错：Failed to get D-Bus connection: Operation not permitted
    #    && systemctl start docker \
    #    && systemctl enable docker
    # 安装 docker-compose
    # 使用注释的两条命令会出现错误：error: open of failed: No such file or directory。可能是 rpm 后面的空格识别问题
    # ​	 && rpm -ivh http://dl.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm \
    # ​    && yum clean all \
        && rpm -ivh http://dl.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm  && yum clean all \
        && yum -y remove epel-release \
        && yum -y install epel-release --enablerepo=extras \
        && yum -y install python-pip \
        && pip install --upgrade pip \
        && yum -y install python-devel \
        && pip install docker-compose
    # 安装 golang
    WORKDIR /opt 
    RUN wget https://dl.google.com/go/go1.11.1.linux-amd64.tar.gz \
        && tar -C /usr/local -xzf go1.11.1.linux-amd64.tar.gz \
    # 安装 nodejs
        && wget https://nodejs.org/dist/v8.16.0/node-v8.16.0-linux-x64.tar.xz \
        && xz -d node-v8.16.0-linux-x64.tar.xz \
        && tar xvf node-v8.16.0-linux-x64.tar \
        && mv node-v8.16.0-linux-x64 /usr/local/node
    ENV GO_HOME=/usr/local/go \
        NODE_HOME=/usr/local/node \
        PATH=/usr/local/go/bin:/usr/local/node/bin:$PATH
```

### 全 RUN 命令

build 后的镜像大小为：2.97G

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbjlfka985j30zn08ct9x.jpg)

```dockerfile
    FROM centos:7.6.1810
# yun install 基础环境
    RUN yum -y install git
    RUN yum -y install curl
    RUN yum -y install	ca-certificates
    RUN yum -y install	make
    RUN yum -y install	gcc
    RUN yum -y install	wget
    RUN yum -y install	yum-utils
    RUN yum update -y

    # 安装 docker-ce
    RUN yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
    RUN yum makecache
    RUN yum -y install docker-ce-18.06.3.ce
    # 启动 docker ce，暂时先不启动 docker，会报错：Failed to get D-Bus connection: Operation not permitted
    #    && systemctl start docker \
    #    && systemctl enable docker
    # 安装 docker-compose
    # 使用注释的两条命令会出现错误：error: open of failed: No such file or directory。可能是 rpm 后面的空格识别问题
    # ​	 && rpm -ivh http://dl.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm \
    # ​    && yum clean all \
    RUN rpm -ivh http://dl.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm  && yum clean all
    RUN yum -y remove epel-release
    RUN yum -y install epel-release --enablerepo=extras
    RUN yum -y install python-pip
    RUN pip install --upgrade pip
    RUN yum -y install python-devel
    RUN pip install docker-compose
# 安装 golang
    WORKDIR /opt 
    RUN wget https://dl.google.com/go/go1.11.1.linux-amd64.tar.gz
    RUN tar -C /usr/local -xzf go1.11.1.linux-amd64.tar.gz
    # RUN echo "export GO_HOME=/usr/local/go" >> /etc/profile
# 安装 nodejs
    RUN wget https://nodejs.org/dist/v8.16.0/node-v8.16.0-linux-x64.tar.xz
    RUN xz -d node-v8.16.0-linux-x64.tar.xz
    RUN tar xvf node-v8.16.0-linux-x64.tar
    RUN mv node-v8.16.0-linux-x64 /usr/local/node
    # RUN echo "export NODE_HOME=/usr/local/node" >> /etc/profile
# go和nodejs的全局bin放在一起，如果分开的话，由于$PATH的原因，会导致后一条命令覆盖前一条命令，从而go环境失效
    # RUN echo "export PATH=/usr/local/go/bin:/usr/local/node/bin:$PATH" >> /etc/profile
# 下面这条指令没有用
# RUN source /etc/profile 的时候，docker 会先跑起来一个容器，然后在容器里运行source /etc/profile，之后把当前的容器commit成新的镜像。
    # RUN source /etc/profile
    ENV GO_HOME=/usr/local/go \
        NODE_HOME=/usr/local/node \
        PATH=/usr/local/go/bin:/usr/local/node/bin:$PATH
```

## 最终交付的 dockerfile1

### 精简 RUN

build 后的镜像大小为：977M

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbk27l1cqsj30u50a5abf.jpg)

```dockerfile
        FROM centos:7.6.1810
    RUN yum -y install git \
        curl \
        ca-certificates \
        make \
        gcc \
        wget \
        yum-utils \
        && yum update -y \
        && yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo \
        && yum makecache \
        && yum -y install docker-ce-18.06.3.ce \
        && rpm -ivh http://dl.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm  && yum clean all \
        && yum -y remove epel-release \
        && yum -y install epel-release --enablerepo=extras \
        && yum -y install python-pip \
        && pip install --upgrade pip \
        && yum -y install python-devel \
        && pip install docker-compose
    WORKDIR /opt 
    RUN wget https://dl.google.com/go/go1.11.1.linux-amd64.tar.gz \
        && wget https://nodejs.org/dist/v8.16.0/node-v8.16.0-linux-x64.tar.xz
```

### 全 RUN

build 后的镜像大小为：2.47G

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbk2csvd75j30wv0a7q4c.jpg)

```dockerfile
    FROM centos:7.6.1810
    RUN yum -y install git
    RUN yum -y install curl
    RUN yum -y install	ca-certificates
    RUN yum -y install	make
    RUN yum -y install	gcc
    RUN yum -y install	wget
    RUN yum -y install	yum-utils
    RUN yum update -y
    RUN yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
    RUN yum makecache
    RUN yum -y install docker-ce-18.06.3.ce
    RUN rpm -ivh http://dl.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm  && yum clean all
    RUN yum -y remove epel-release
    RUN yum -y install epel-release --enablerepo=extras
    RUN yum -y install python-pip
    RUN pip install --upgrade pip
    RUN yum -y install python-devel
    RUN pip install docker-compose
    WORKDIR /opt 
    RUN wget https://dl.google.com/go/go1.11.1.linux-amd64.tar.gz
    RUN wget https://nodejs.org/dist/v8.16.0/node-v8.16.0-linux-x64.tar.xz
```
