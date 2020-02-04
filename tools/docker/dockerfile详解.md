# dockerfile 详解

Dockerfile 构建镜像的过程：

1. 从 base 镜像运行一个容器。
2. 执行一条指令，对容器做修改。
3. 执行类似 docker commit 的操作，生成一个新的镜像层。
4. Docker 再基于刚刚提交的镜像运行一个新容器。
5. 重复 2-4 步，直到 Dockerfile 中的所有指令执行完毕。

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbiy9ni187j30z70py7e7.jpg)

**例子**：

```dockerfile
FROM centos:latest
LABEL maintainer="yangge <yangge@qf.com>"  description="Install tree vim*"
RUN rpm -qa | grep tree || yum  install -y tree vim*
```

**指令介绍**：

FORM 定义一个基础镜像

LABEL 定义一些元数据信息，比如作者、版本、关于镜像的描述信息

RUN 执行命令行的命令

**构建镜像指令**：

格式：`docker bulid -t 仓库名/镜像名:tag .`

例子：`docker build -t centos:1.20 .`

这个 `.`  表示当前目录，这实际上是在指定上下文的目录是当前目录，`docker build` 命令会将该目录下的内容打包交给 Docker 引擎以帮助构建镜像。

`docker build` 命令得知这个路径后，会将路径下的所有内容打包，然后上传给 Docker 引擎。这样 Docker 引擎收到这个上下文包后，展开就会获得构建镜像所需的一切文件。

## 指令

### FROM 指令

主要作用是指定一个镜像作为构建自定义镜像的基础镜像，在这个基础镜像之上进行修改定制。

这个指令是 Dockerfile 中的必备指令，同时也必须是第一条指令。

在 [Docker Store](https://store.docker.com/) 上有很多高质量的官方镜像，可以直接作为我们的基础镜像。

作为服务类的，如 [Nginx](https://store.docker.com/images/nginx/)  [Mongo](https://store.docker.com/images/mongo/) 等

用于开发的， 如 [Python](https://store.docker.com/images/python/)  [golang](https://store.docker.com/images/golang/)

操作系统类， 如 [Centos](https://store.docker.com/images/centos/)  [ubuntu](https://store.docker.com/images/ubuntu/)

除了一些现有的镜像，Docker 还有一个特殊的镜像 `scratch`

这个镜像是虚拟的，表示空白镜像

```undefined
FORM scratch
...
```

这以为着这将不以任何镜像为基础镜像。

可以把可执行的二进制文件复制到镜像中直接执行，容器本身就是和宿主机共享 Linux内核的。

使用 Go 语言开发的应用很多会使用这种方式来制作镜像，这也是为什么有人认为 Go 是特别适合容器微服务架构的语言的原因之一。

### ADD 指令

`CMD 在docker run 时运行的`

如：`ADD hello /`

ADD 是把当前目录下的 hello 文档拷贝到 容器中的根目录下

### CMD 指令

如：`CMD ["/hello"]`

CMD 执行根目录下的 hello 文件

`Dockerfile` 中只能有一条`CMD`指令。如果列出多个，`CMD` 则只有最后一个`CMD`会生效。

**CMD 主要目的是为运行容器时提供默认值**

Docker 不是虚拟机，容器就是进程，`CMD` 指令就是用于指定默认的容器主进程的启动命令的。在启动(运行)一个容器时可以指定新的命令来替代镜像设置中的这个默认命令。

可以包含可执行文件，当然也可以省略。

`CMD` 指令的格式和 `RUN` 相似，也是两种格式：

- `shell` 格式：`CMD <命令>`
- `exec` 格式：`CMD ["可执行文件", "参数1", "参数2"...]`
- 参数列表格式：`CMD ["参数1", "参数2"...]`。在指定了 `ENTRYPOINT` 指令后，用 `CMD` 指定具体的参数。

> **注意**：不要混淆`RUN` 和 `CMD`。`RUN`实际上运行一个命令并提交结果; `CMD`在构建时不执行任何操作，但指定镜像的默认命令。

### ENTRYPOINT 指令

`ENTRYPOINT` 的目的和 `CMD` 一样，都是在指定容器的启动程序及参数。

`ENTRYPOINT` 在运行时也可以被替代，不过比 `CMD` 要略显繁琐，需要通过 `docker run` 的参数 `--entrypoint` 来指定。

`ENTRYPOINT` 的格式和 `RUN` 指令格式一样，也分为 `exec` 格式和 `shell` 格式。

当指定了 `ENTRYPOINT` 后，`CMD` 的含义就发生了改变，不再是直接的运行其命令，而是将 `CMD` 的内容作为参数传给 `ENTRYPOINT` 指令，也就是实际执行时，将变为：

```bash
<ENTRYPOINT> "<CMD>"
```

### LABEL 指令

LABEL 指令用于指定一个镜像的描述信息

该`LABEL`指令将元数据添加到镜像中。

`LABEL`是一个键值对。

要在`LABEL`值中包含空格，请像在命令行解析中一样使用引号和续行符`\`。

几个用法示例：

```csharp
LABEL maintainer="yangge@qf.com"
LABEL "com.example.vendor"="ACME Incorporated"
LABEL com.example.label-with-value="foo"
LABEL version="1.0"
LABEL description="This text illustrates \
that label-values can span multiple lines."
```

一个镜像可以有多个`LABEL`标签。您可以在一行中指定多个标签。并且目前的版本不再会影响到镜像的大小了。

但是仍然可以把他们写在一行或用反斜线进行续航

```bash
LABEL multi.label1="value1" multi.label2="value2" other="value3"
```

```bash
LABEL multi.label1="value1" \
      multi.label2="value2" \
      other="value3"
```

**有继承关系的镜像，标签也会有面向对象编程中继承的关系和特性**

要查看镜像的 `LABEL` 信息，请使用该`docker inspect`命令。

### ENV 指令

**用于设置环境变量**

格式有两种：

- `ENV  `
- `ENV = =...`

推荐的方式，易读

```csharp
ENV VERSION=1.0 DEBUG=on \
 NAME="Happy Feet"
```

其他指令使用：

```bash
RUN echo $VERSION
...
```

下列指令可以支持环境变量： `ADD`、`COPY`、`ENV`、`EXPOSE`、`LABEL`、`USER`、`WORKDIR`、`VOLUME`、`STOPSIGNAL`、`ONBUILD`。

### RUN 指令

`RUN 是在 docker build 时运行的`

`RUN` 指令是在容器内执行 shell 命令，默认会是用 `/bin/sh -c` 的方式执行。

**执行命令的两种方式**：

- `RUN `（*shell*形式，该命令在shell中运行）
- `RUN ["executable", "param1", "param2"]`（*exec*形式）

之前说过，Dockerfile 中每一个指令都会建立一层，`RUN` 也不例外。每一个 `RUN` 的行为，就和刚才我们手工建立镜像的过程一样：新建立一层，在其上执行这些命令，执行结束后，`commit` 这一层的修改，构成新的镜像。

所以，在使用 *shell* 方式，尽量多的使用续行符`\`

```dart
RUN /bin/bash -c 'source $HOME/.bashrc; \
 echo $HOME'
```

写 Dockerfile 的时候，要经常提醒自己，这并不是在写 Shell 脚本，而是在定义每一层该如何构建。

注意当使用 `exec` 方式时，需要明确指定 `shell` 路径，否则变量可能不会生效

```bash
FROM centos
ENV name="yangge"
RUN ["/bin/echo", "$name"]
```

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbixkf26gkj30vm0g21bs.jpg)

> 可以看到 `$name` 被作为普通的字符串输出了，因为 `$name` 是 shell 中的用法，而这里里并没有 使用到 shell

**下面是正确的做法**:

```bash
FROM alpine
ENV name="yangge"
RUN ["/bin/sh", "-c", "/bin/echo $name"]
```

> **注意:** *exec*的方式下，列表中的内容会被解析为JSON数组，这意味着您必须在单词周围使用双引号（“）  而非单引号（'）。

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbixm9ozszj30vj0ehh1o.jpg)

### WORKDIR 指令

用于声明当前的工作目录，以后各层的当前目录就被改为指定的目录。

格式为 `WORKDIR <工作目录路径>`。

如该目录不存在，`WORKDIR` 会帮你建立目录。

再次强调！不要以为编写 `Dockerfiel` 是在写 `shell` 脚本。

下面是一个错误示例：

```dockerfile
RUN cd /app
RUN echo "hello" > world.txt
```

如果将这个 `Dockerfile` 进行构建镜像运行后，会发现找不到 `/app/world.txt` 文件，或者其内容不是 `hello`。

原因其实很简单，这两行 `RUN` 命令的执行环境根本不同，是两个完全不同的容器。这就是对 `Dockerfile` 构建分层存储的概念不了解所导致的错误。

之前说过每一个 `RUN` 都是启动一个容器、执行命令、然后提交存储层文件变更。

两行 `RUN` 分别构建了并启动了各自全新的容器。

因此如果需要改变以后各层的工作目录的位置，那么应该使用 `WORKDIR` 指令。

```dockerfile
FROM alpine
WORKDIR /a/b
RUN touch a_b_f.txt
WORKDIR /a
RUN touch a_f.txt
```

```shell
[root@localhost workdir]# docker run -it alpine:workdir /bin/sh
/a # ls
a_f.txt  b
/a # cd b
/a/b # ls
a_b_f.txt
```

### COPY 指令

格式：

- `COPY <源路径>... <目标路径>`

- `COPY ["<源路径1>",... "<目标路径>"]`

  和 `RUN` 指令一样，也有两种格式，一种类似于命令行，一种类似于函数调用。

`<目标路径>` 可以是容器内的绝对路径，也可以是相对于 `WORKDIR` 指定的工作目录的相对路径。目标路径不需要事先创建，如果目录不存在会在复制文件前先被创建。

`COPY` 指令将会从构建的上下文目录中，把源路径的文件或目录复制到新的一层的镜像内的 `<目标路径>` 位置。比如：

```dockerfile
COPY qf.json /usr/src/app/
```

注意下面是错误的

```dockerfile
COPY qf.json /usr/src/app
```

这样会把 `qf.json` 拷贝成为 `/usr/src/` 目录下的 `app` 文件

### USER 指令

`USER` 则是改变执行 `RUN`, `CMD` 以及 `ENTRYPOINT` 这类命令的身份。

这个用户必须是事先在容器内存在(建立好)的，否则无法切换。

如果以 `root` 执行的脚本，在执行期间希望改变身份，比如希望以某个已经建立好的用户来运行某个服务进程，不要使用 `su` 或者 `sudo`，这些都需要比较麻烦的配置，而且在 TTY 缺失的环境下经常出错。建议使用 [`gosu`](https://github.com/tianon/gosu)。

### EXPOSE 指令

暴露容器的端口

docker run 的时候指定 -P 或者 -p 将容器的端口映射到宿主机上。这样外界访问宿主机就可以获取到容器提供的服务了。

-P（大写） 命令可以结合这个dockerfile文件中的EXPOSE暴露的端口。会将容器中的EXPOSE端口随机映射到宿主机的端口。

dockerfile 例子：

```dockerfile
FROM nginx
EXPOSE 80
```

- `docker run -d --net=host image:tag`，使用宿主机的网络端口（80）、不存在映射的关系。

  ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbj8l1t743j318s04btcu.jpg)

- `docker run -d -p image:tag`，容器内部 80 端口随机映射到宿主机的端口

  ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbj8p41aetj318q04gwj0.jpg)

**参考**：

- [Dockerfile 的详解](Dockerfile 的详解)
- [Dockerfile指令详解](https://www.cnblogs.com/jing1617/p/9561922.html)
- [菜鸟教程](https://www.runoob.com/docker/docker-dockerfile.html)
- [Dockerfile中的expose到底有啥用](https://blog.csdn.net/finalheart/article/details/100751447)

## 如何调试 dockerfile

通过 Dockerfile 构建镜像的过程：

1. 从 base 镜像运行一个容器。
2. 执行一条指令，对容器做修改。
3. 执行类似 docker commit 的操作，生成一个新的镜像层。
4. Docker 再基于刚刚提交的镜像运行一个新容器。
5. 重复 2-4 步，直到 Dockerfile 中的所有指令执行完毕。

从这个过程可以看出，如果 Dockerfile 由于某种原因执行到某个指令失败了，我们也将能够得到前一个指令成功执行构建出的镜像，这对调试 Dockerfile 非常有帮助。我们可以**运行最新的这个镜像定位指令失败的原因**。

如果 Dockerfile 在执行第三步 RUN 指令时失败。我们可以利用第二步创建的镜像 22d31cc52b3e 进行调试，方式是通过 `docker run -it` 启动镜像的一个容器。

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbjiw1j2btj318d0c6dkh.jpg)

**参考**：[调试 Dockerfile - 每天5分钟玩转 Docker 容器技术（15）](https://www.cnblogs.com/CloudMan6/p/6853329.html)