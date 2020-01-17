# go环境搭建

## 1. windows 平台

### 1.1 go 编译器

`.msi` 后缀文件安装之后，将对应的 `bin` 目录添加至 `PATH` 环境变量中
`shell` 输入 `go version` 检测是否安装成功

![2](http://ww1.sinaimg.cn/large/006alGmrly1g23d0kynizj30z90iodvf.jpg)

![2](http://ww1.sinaimg.cn/large/006alGmrly1g23cufj37yj313r0k840x.jpg)

![2](http://ww1.sinaimg.cn/large/006alGmrly1g23cxnm6nmj313t0qwgph.jpg)**参考**：[http://www.runoob.com/go/go-environment.html](http://www.runoob.com/go/go-environment.html)

### 1.2 设置 GOPATH

- 手动在 go 解压的文件夹中创建 GOPATH 文件夹。然后在 GOPATH 文件夹中创建三个文件夹，分别为 bin、pkg 和 src

  [![1SDZ5V.md.png](https://s2.ax1x.com/2020/01/17/1SDZ5V.md.png)](https://imgchr.com/i/1SDZ5V)

- 将 GOPATH 添加至系统环境变量 PATH 中

  [![1SDaxe.md.png](https://s2.ax1x.com/2020/01/17/1SDaxe.md.png)](https://imgchr.com/i/1SDaxe)

  之后通过 `go get `命令下载的包就会放置在 `GOPATH`路径中

### 1.3 集成开发工具 goLand

`.exe` 正常安装即可，注意安装目录

添加 `GOPATH` 环境变量，`file/setting` 中添加

[![1SDsat.md.png](https://s2.ax1x.com/2020/01/17/1SDsat.md.png)](https://imgchr.com/i/1SDsat)

---

## 2. ubuntu 平台

### 2.1 go 编译器

#### 2.1.1 下载安装包

[https://golang.google.cn/dl/](https://golang.google.cn/dl/)

#### 2.1.2 安装

将 `go1.11.5.linux-amd64.tar.gz` 解压至 `/usr/local`

```shell
tar -C /usr/local -xzf go1.11.5.linux-amd64.tar.gz
```

##### 2.1.2.1 临时添加环境变量(只在当前 bash 中生效)

```shell
export PATH=$PATH:/usr/local/go/bin
```

##### 2.1.2.2 永久添加环境变量，修改配置文件

```shell
vi /etc/profile
```

然后在最后一行插入：

```shell
export PATH=$PATH:/usr/local/go/bin
```

运行如下命令，让修改在当前 bash 中生效，==注意是当前==，如果要一直生效需要重启电脑

```shell
source /etc/profile
```

**参考**：[
Linux中修改环境变量及生效方法（永久、临时）环境变量查看](https://blog.csdn.net/u011630575/article/details/49839893)

#### 2.1.3 查看是否配置成功：

```shell
go version
```

参考至：[https://golang.google.cn/doc/install?download=go1.11.5.linux-amd64.tar.gz](https://golang.google.cn/doc/install?download=go1.11.5.linux-amd64.tar.gz)

### 2.2 集成开发工具 goLand

下载地址：[https://www.jetbrains.com/go/download/#section=linux](https://www.jetbrains.com/go/download/#section=linux)

使用和安装教程：[https://blog.csdn.net/Real_Myth/article/details/74085114](https://blog.csdn.net/Real_Myth/article/details/74085114)