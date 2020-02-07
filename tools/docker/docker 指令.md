# docker 指令

## docker 本地导出和导入

**参考**：[Docker images导出和导入](https://www.jianshu.com/p/8408e06b7273)

---

## docker save与docker export的区别、以及load和import的区别

**简单普及一下，docker的save保存镜像(load与其对应)，docker的export导出容器(import与其对应)，但是两者load/import的时候都是生成镜像。**

**docker save**：用来将一个或多个image打包保存

如：`docker save -o images.tar postgres:9.6 mongo:3.4`

打包之后的`images.tar`包含`postgres:9.6`和`mongo:3.4`这两个镜像

**docker export**：用来将container的文件系统进行打包

如：`docker export -o postgres-export.tar postgres`

**load和import**：用户既可以使用 docker load 来导入`镜像存储文件`到本地镜像库，也可以使用 docker import 来导入一个`容器快照`到本地镜像库。这两者的区别在于容器快照文件将丢弃所有的历史记录和元数据信息（即仅保存容器当时的快照状态），而镜像存储文件将保存完整记录，体积也要大。此外，从容器快照文件导入时可以重新指定标签等元数据信息

**参考**：

- [docker save与docker export的区别](https://blog.csdn.net/liukuan73/article/details/78089138)
- [Docker之export/import使用](https://blog.csdn.net/weixin_42003671/article/details/86614577)

---

## docker commit

将容器打包成镜像

`docker commit -a "runoob.com" -m "my apache" 容器名称或id 打包的镜像名称:标签`

OPTIONS说明：
-a :提交的镜像作者；
-c :使用Dockerfile指令来创建镜像；
-m :提交时的说明文字；
-p :在commit时，将容器暂停。

**参考**：[docker 将正在运行的容器打包为镜像](https://www.cnblogs.com/jackadam/p/9528448.html)

---

## docker cp 用法

==注意如果从 linux 容器拷贝链接格式的文件至 windows 本地，则会出错。可以手动将 linux 容器中的链接删掉或者换一种形式表示链接==

从本地复制文件至容器或从容器复制文件至本地

如从容器复制到本地：`docker cp eb768ea5da98:/opt/go1.11.1.linux-amd64.tar.gz C:\Users\qiubing\Desktop\dockerfile`

![image.png](https://ww1.sinaimg.cn/large/006alGmrly1gbls6sjibhj312y05lt9f.jpg)

注意容器需要正在运行

---

## docker stop 与 docker kill的区别

docker stop，支持“优雅退出”。先发送SIGTERM信号，在一段时间之后（10s）再发送SIGKILL信号。Docker内部的应用程序可以接收SIGTERM信号，然后做一些“退出前工作”，比如保存状态、处理当前请求等。

docker kill，发送SIGKILL信号，应用程序直接退出。

> kill是不管容器同不同意，我直接执行kill -9，强行终止；stop的话，首先给容器发送一个TERM信号，让容器做一些退出前必须的保护性、安全性操作，然后让容器自动停止运行，如果在一段时间内，容器还是没有停止，再进行kill -9，强行终止。

**参考**：

- [docker stop 与 docker kill的区别](https://www.bbsmax.com/A/gVdnR6NEdW/)

- [docker kill 和 stop 有什么区别](http://dockone.io/question/158)

---
