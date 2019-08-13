# centos 下配置环境

**参考**：

1. [如何在本地编译Fabric Code](https://www.cnblogs.com/studyzy/p/8030613.html)

2. [源码编译Fabric](http://keliu.me/2018/11/29/FabricCode/)

3. [git fabric 1.0.0 官方源码进行编译，生成 docker images](https://blog.csdn.net/remote_roamer/article/details/70228662)

4. [CentOS 7 搭建第一个Fabric网络](https://blog.csdn.net/line_aijava/article/details/80749052)

## 1. 安装 docker-compose 时出错

![2](http://ww1.sinaimg.cn/large/006alGmrgy1g5wza7pd6rj31cp0finct.jpg)

解决方法：`pip install --upgrade setuptools`

**参考**：[安装tldr出错ERROR: Command errored out with exit status 1: python setup.py egg_info Check the logs for f](https://blog.csdn.net/qq_37189082/article/details/97658103)

## 2. make docker时出错。。。cp: cannot stat ‘build/docker/gotools/bin/protoc-gen-go’: No such file or directory

[https://stackoverflow.com/questions/41040156/cp-cannot-stat-build-docker-gotools-bin-protoc-gen-go-no-such-file-or-direct](https://stackoverflow.com/questions/41040156/cp-cannot-stat-build-docker-gotools-bin-protoc-gen-go-no-such-file-or-direct)