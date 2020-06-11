# 快速安装

## 环境要求

- Go开发环境

  	- Golang >= 1.13.6
   - 项目采用go module进行包管理。可参考 [Using Go Modules](https://blog.golang.org/using-go-modules)

  如果您没有部署过Go环境，可以参考 [官方文档](https://golang.org/doc/)

- 基本开发组件

  - Git（Windows、Linux及MacOS需要）
  - Git bash（仅Windows需要

- FISCO BCOS节点：请参考 [FISCO BCOS安装](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/installation.html#fisco-bcos) 搭建

## 部署Go SDK

### 1. 拉取源代码

```shell
git clone https://github.com/FISCO-BCOS/go-sdk.git
```

### 2. 安装依赖项

进入go-sdk目录，执行命令：

```shell
go mod download
```

