# go安装

## 1. 下载安装包

[https://golang.google.cn/dl/](https://golang.google.cn/dl/)

## 2. 安装

将 `go1.11.5.linux-amd64.tar.gz` 解压至 `/usr/local`

```shell
tar -C /usr/local -xzf go1.11.5.linux-amd64.tar.gz
```

### 2.1 临时添加环境变量(只在当前 bash 中生效)

```shell
export PATH=$PATH:/usr/local/go/bin
```

### 2.2 永久添加环境变量，修改配置文件

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

### 2.3 查看是否配置成功：

```shell
go version
```

参考至：[https://golang.google.cn/doc/install?download=go1.11.5.linux-amd64.tar.gz](https://golang.google.cn/doc/install?download=go1.11.5.linux-amd64.tar.gz)