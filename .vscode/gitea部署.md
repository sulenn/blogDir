# gitea 部署

## ubuntu 18.04

- `mkdir $GOPATH/src/code.gitea.io/gitea`

- `cd $GOPATH/src/code.gitea.io/gitea`

- `git clone https://github.com/go-gitea/gitea.git`

- `git branch -a` && `git checkout v1.10.1`

**环境要求**：

go 1.11.0 或以上版本

node 10.0.0 或以上版本，并且安装 npm

make

**编译**：

1.10.0 版本开始用如下命令编译

`TAGS="bindata sqlite sqlite_unlock_notify" make generate build`

**运行测试**：

`./gitea web`

**参考**：

- [从源代码安装](https://docs.gitea.io/zh-cn/install-from-source/)

- [Installation from source](https://github.com/go-gitea/gitea/blob/release/v1.10/docs/content/doc/installation/from-source.en-us.md#build)

---
