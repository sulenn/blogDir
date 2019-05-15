# 包管理工具 glide

glide是Golang的包管理工具，是为了解决Golang依赖问题的。 为什么需要glide？ 原因很简单，Go 语言原生包管理的缺陷。罗列一下golang的 get 子命令管理依赖有很多大缺陷：

- 能拉取源码的平台很有限，绝大多数依赖的是 github.com

- 不能区分版本，以至于令开发者以最后一项包名作为版本划分

- 依赖 列表/关系 无法持久化到本地，需要找出所有依赖包然后一个个 go get

- 只能依赖本地全局仓库（GOPATH/GOROOT），无法将库放置于局部仓库（$PROJECT_HOME/vendor）

**glide 安装**：

```shell
go get github.com/Masterminds/glide
go install github.com/Masterminds/glide
```

**初始化**:

初始化之前需要先进入到项目目录

`cd $GOPATH/src/procectName`

执行初始化

`glide init`

在初始化过程中， glide 会询问一些问题。 glide.yaml记载了依赖包的列表及其更新规则，每次执行 glide up 时，都会按照指定的规则（如只下载补丁(patch)不下载升级(minor)）下载新版。

**安装依赖**：

`glide install`

**升级依赖版本**：

`glide up`

**添加并下载依赖**：
`glide get github.com/labstack/gommon`

**使用镜像**：

可以设置使用其他网络地址作为镜像，也可以使用本地文件作为镜像

```shell
glide mirror set golang.org/x/crypto github.com/golang/crypto
glide mirror set golang.org/x/text /home/users/qiangmzsx/var/golang/golang.org/x/text
```

**参考**：[https://blog.csdn.net/longshengguoji/article/details/85940507](https://blog.csdn.net/longshengguoji/article/details/85940507)
