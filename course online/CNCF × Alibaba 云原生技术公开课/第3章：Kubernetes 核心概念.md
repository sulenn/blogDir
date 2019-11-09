# 第三章：Kubernetes 核心概念

工业级的容器编排平台

- Kubernetes 这个单词是希腊语，它的中文翻译是“舵手”或者“飞行员”。

- 一些常见的资料中也会看到“ks”这个词，也就是“k8s”，它是通过将8个字母“ubernete ”替换为“8”而导致的一个缩写。

`自动化的容器编排平台`: 负责应用的部署、应用的弹性以及应用的管理

## kubernates 核心功能

1. 服务发现与负载均衡

2. 容器自动装箱：也叫 scheduling，调度。把一个容器放到一个集群的某一个机器上，Kubernetes 会帮助我们去做存储的编排，让存储的生命周期与容器的生命周期能有一个连接；

    Kubernetes 可以把用户提交的容器放到 Kubernetes 管理的集群的某一台节点上去。Kubernetes 的调度器是执行这项能力的组件，它会观察正在被调度的这个容器的大小、规格。

    比如说它所需要的 CPU以及它所需要的 memory，然后在集群中找一台相对比较空闲的机器来进行一次 placement，也就是一次放置的操作。在这个例子中，它可能会把红颜色的这个容器放置到第二个空闲的机器上，来完成一次调度的工作。

3. 存储编排

4. 自动容器恢复

    Kubernetes 有一个节点健康检查的功能，它会监测这个集群中所有的宿主机，当宿主机本身出现故障，或者软件出现故障的时候，这个节点健康检查会自动对它进行发现。

    下面 Kubernetes 会把运行在这些失败节点上的容器进行自动迁移，迁移到一个正在健康运行的宿主机上，来完成集群内容器的一个自动恢复

5. 自动发布与回滚

6. 配置与密文管理

7. 批量执行

8. 水平伸缩

    Kubernetes 有业务负载检查的能力，它会监测业务上所承担的负载，如果这个业务本身的 CPU 利用率过高，或者响应时间过长，它可以对这个业务进行一次扩容。

    比如说在下面的例子中，黄颜色的过度忙碌，Kubernetes 就可以把黄颜色负载从一份变为三份。接下来，它就可以通过负载均衡把原来打到第一个黄颜色上的负载平均分到三个黄颜色的负载上去，以此来提高响应的时间。

## Kenernetes 架构

### Master

![image.png](https://ws1.sinaimg.cn/large/006alGmrgy1g8rh6z2ap4j30nm0ka436.jpg)

### Node

![image.png](https://ws1.sinaimg.cn/large/006alGmrgy1g8rh8z26qpj30vn0k9tfd.jpg)

### 例子

![image.png](https://ws1.sinaimg.cn/large/006alGmrgy1g8rhc0dgicj310m0eedj6.jpg)

## Kubernetes 的核心概念与它的 API

### 核心概念 - Pod

1. 最小的调度以及资源单元

2. 由一个或者多个容器组成

3. 定义容器运行的方式（Command、环境变量等）

4. 提供给容器共享的运行环境（网络、进程空间）

![image.png](https://ws1.sinaimg.cn/large/006alGmrgy1g8rhfdh2saj30cs0d2762.jpg)

### 核心概念 - Volume

1. 声明再 Pod 中的容器可访问的文件目录

2. 可以被挂载在 Pod 中一个（或者多个）容器的制定路径下

3. 支持多种后端存储的抽象：本地存储、分布式存储、云存储

![image.png](https://ws1.sinaimg.cn/large/006alGmrgy1g8rhhjsp4sj30710dxt9m.jpg)

### 核心概念 - Deployment

1. 定义一组 Pod 的副本数目、版本等

2. 通过控制器（Controller）维持 Pod 的数目

    - 自动回复失败的 Pod

3. 通过控制器以制定的策略控制版本

    - 滚动升级、重新生成、回滚等

![image.png](https://ws1.sinaimg.cn/large/006alGmrgy1g8rhkd7bv8j30e607baav.jpg)

### 核心概念 - Service

1. 提供访问一个或多个 Pod 实例的稳定访问地址

2. 支持多种访问方式实现

    - ClisterIP

    - NodePort

    - LoadBalancer

![image.png](https://ws1.sinaimg.cn/large/006alGmrgy1g8rhzrnfukj30dc0df402.jpg)

### 核心概念 - Namespqces

1. 一个集群内部的逻辑隔离机制（鉴权、资源额度）

2. 每个资源都属于一个 Namespace

3. 同一个 Namespaces 中的资源命名唯一

4. 不同 Namespace 中的资源可重名

![image.png](https://ws1.sinaimg.cn/large/006alGmrgy1g8ri1rxdu7j30f907040k.jpg)
