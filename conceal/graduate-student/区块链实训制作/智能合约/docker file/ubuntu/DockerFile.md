[toc]

# DockerFile

## ethereum

```dockerfile
    FROM ubuntu:18.04
# 更换源
    RUN sed -i 's#http://archive.ubuntu.com/#http://mirrors.tuna.tsinghua.edu.cn/#' /etc/apt/sources.list
# 安装geth
    RUN apt-get update; apt-get install -y software-properties-common; add-apt-repository -y ppa:ethereum/ethereum; apt-get update; apt-get install -y ethereum
# 安装nodejs和npm（顺便安装vim、git和curl）
    RUN apt-get install -y vim git curl nodejs npm
# 安装 solc
    RUN apt-get install -y solc
```

**参考**：

- https://blog.csdn.net/m0_37847176/article/details/82983519
- https://www.pianshen.com/article/6524293687/

## solidity

```dockerfile
    FROM ubuntu:18.04
# 添加 solidity 编译器
    ADD ./solc-0.6.1 /home/solc-0.6.1
```

