# go安装

## 1. 下载安装包

[https://golang.google.cn/dl/](https://golang.google.cn/dl/)

## 2. 安装

将 `go1.11.5.linux-amd64.tar.gz` 解压至 `/usr/local`

```shell
tar -C /usr/local -xzf go1.11.5.linux-amd64.tar.gz
```

添加环境变量

```shell
export PATH=$PATH:/usr/local/go/bin
```

参考至：[https://golang.google.cn/doc/install?download=go1.11.5.linux-amd64.tar.gz](https://golang.google.cn/doc/install?download=go1.11.5.linux-amd64.tar.gz)