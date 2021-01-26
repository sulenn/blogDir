# Docker

Docker 是一个开源的应用容器引擎

## 隔离机制

1. namespace：隔离

2. cgroups：资源分配

**参考**：[docker资源隔离实现方式](https://www.cnblogs.com/--smile/p/11810027.html)

---

## docker 和虚拟机比较

虚拟机也是一种虚拟化技术，它与 Docker 最大的区别在于它是通过模拟硬件，并在硬件上安装操作系统来实现。

1. 启动速度

    - 启动虚拟机需要先启动虚拟机的操作系统，再启动应用，这个过程非常慢；

    - 而启动 Docker 相当于启动宿主操作系统上的一个进程。

2. 占用资源

    - 虚拟机是一个完整的操作系统，需要占用大量的磁盘、内存和 CPU 资源，一台机器只能开启几十个的虚拟机。

    - 而 Docker 只是一个进程，只需要将应用以及相关的组件打包，在运行时占用很少的资源，一台机器可以开启成千上万个 Docker。

**参考**：[https://github.com/CyC2018/CS-Notes/blob/master/notes/Docker.md](https://github.com/CyC2018/CS-Notes/blob/master/notes/Docker.md)

---

## docker 的优势

除了启动速度快以及占用资源少之外，Docker 具有以下优势：

1. 更容易迁移

    提供一致性的运行环境。已经打包好的应用可以在不同的机器上进行迁移，而不用担心环境变化导致无法运行。

2. 更容易维护

    使用分层技术和镜像，使得应用可以更容易复用重复的部分。复用程度越高，维护工作也越容易。

3. 更容易扩展

    可以使用基础镜像进一步扩展得到新的镜像，并且官方和开源社区提供了大量的镜像，通过扩展这些镜像可以非常容易得到我们想要的镜像。

**参考**：[https://github.com/CyC2018/CS-Notes/blob/master/notes/Docker.md](https://github.com/CyC2018/CS-Notes/blob/master/notes/Docker.md)

---
