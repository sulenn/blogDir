# 第15章：深入剖析 Linux 容器

容器时一种轻量级的虚拟化技术，因为它和虚拟机比起来少了一层 hypervisor 层

linux 容器中使用 cgroup 和 namespace 来保证进程所用到的资源是被隔离和限制的。

![image.png](https://ws1.sinaimg.cn/large/006alGmrly1g8s4io6mfcj30zl0iftkc.jpg)

![image.png](https://ws1.sinaimg.cn/large/006alGmrly1g8s4pxaj97j30wz0ergst.jpg)