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

## 可能出现的错误

### 1. open conf/locale/locale_en-US.ini: no such file or directory

**version：1.10.1**:

通过 goland 直接运行 main.go。出现错误：`open conf/locale/locale_en-US.ini: no such file or directory`

解决方法：在项目根路径中创建`conf`文件夹，然后从`./option/`将`locale`文件夹和里面的文件一起粘贴至`conf`文件夹中即可

**参考**：[open conf/locale/locale_en-US.ini: no such file or directory after the first start](https://github.com/go-gitea/gitea/issues/535)

### 2. `http://0.0.0.0:3000/install` 页面初始配置问题

**错误**：`数据库设置无效: Error 1049: Unknown database 'gitea'`

![image.png](http://ww1.sinaimg.cn/large/006alGmrgy1gajqqbj4h0j30s70hcwfp.jpg)

**解决方法**：手动在数据库中创建一个名为 `gitea` 的数据库。`create database gitea`

### 3. 访问 `http://localhost:3000/` 出现错误 `html/template: "install" is undefined`

![image.png](http://ww1.sinaimg.cn/large/006alGmrgy1gao9by8heij30e7060aa6.jpg)

**解决方案**：

`modules/setting/setting.go` 第 408 行添加 `os.Args = []string {"./gitea", "web"}`

![image.png](http://ww1.sinaimg.cn/large/006alGmrgy1gao9eciz02j30nt082t9p.jpg)

---
