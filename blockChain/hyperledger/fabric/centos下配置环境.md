# centos 下配置环境

包括编译 fabric 源码，和运行 fabric-samples 两部分测环境

**参考**：

1. [如何在本地编译Fabric Code](https://www.cnblogs.com/studyzy/p/8030613.html)

2. [源码编译Fabric](http://keliu.me/2018/11/29/FabricCode/)

3. [git fabric 1.0.0 官方源码进行编译，生成 docker images](https://blog.csdn.net/remote_roamer/article/details/70228662)

4. [CentOS 7 搭建第一个Fabric网络](https://blog.csdn.net/line_aijava/article/details/80749052)

## 1. 安装 docker-compose 时出错

![2](http://ww1.sinaimg.cn/large/006alGmrgy1g5wza7pd6rj31cp0finct.jpg)

解决方法：`pip install --upgrade setuptools`

**参考**：[安装tldr出错ERROR: Command errored out with exit status 1: python setup.py egg_info Check the logs for f](https://blog.csdn.net/qq_37189082/article/details/97658103)

---

## 2. make docker时出错。。。cp: cannot stat ‘build/docker/gotools/bin/protoc-gen-go’: No such file or directory

[https://stackoverflow.com/questions/41040156/cp-cannot-stat-build-docker-gotools-bin-protoc-gen-go-no-such-file-or-direct](https://stackoverflow.com/questions/41040156/cp-cannot-stat-build-docker-gotools-bin-protoc-gen-go-no-such-file-or-direct)

---

## 3. PyYAML 可能由于版本导致的问题

PyYAML 这个东西，直接用 `pip install pyyaml` 下载的话版本过高，会导致在运行 `./byfn.sh up` 出错。

![2](http://ww1.sinaimg.cn/large/006alGmrgy1g5zjga1hw3j30w307w76v.jpg)

卸载的话也不能 `pip uninstall pyyaml` 直接卸载，出错

![2](http://ww1.sinaimg.cn/large/006alGmrgy1g5zjheaxs4j319c01b0t4.jpg)

**卸载方法**：

1. `cd /home/qiubing/anaconda3/lib/python3.7/site-packages` （pyyaml 所在目录，通过 `pip show`查看）

2. 删掉所有和 `pyyaml` 相关的东西。

    `rm -rf PyYAML-3.13.dist-info`
    `rm -rf PyYAML-5.1-py3.7.egg-info`

卸载后重装

![2](http://ww1.sinaimg.cn/large/006alGmrgy1g5zjklk0kkj315b06ndib.jpg)

显示版本过高，和docker-compose不兼容，不过暂时能用，如果下次用不了就尝试下载符合版本要求的pyyaml

---

## 4. fatal error: unexpected signal during runtime execution   ==[signal SIGSEGV: segmentation violation code=0x1 addr=xxx pc=xxx]==

![image.png](https://ws1.sinaimg.cn/large/006alGmrly1g7nltx106kj30xs0h50vb.jpg)

这个问题困扰了我好几天。。真是浪费时间

**解决方法**：修改自己阿里云ecs机器里面的 `/etc/resolv.conf`，把里面的 `options timeout:2 attempts:3 rotate single-request-reopen` 这一行内容注释掉 :

**参考：**

- [Hyperledger Fabric在阿里云安装出现的一些问题解决](https://ywandy.github.io/Fabric%E5%9C%A8%E9%98%BF%E9%87%8C%E4%BA%91%E5%AE%89%E8%A3%85%E5%87%BA%E7%8E%B0%E7%9A%84%E4%B8%80%E4%BA%9B%E9%97%AE%E9%A2%98%E8%A7%A3%E5%86%B3.html)

- [阿里云centos7.4运行hyperledger fabric1.3.0/1.4.0的fabric-sample（first_network）](https://blog.csdn.net/destiny_aqua/article/details/86570917)

---
