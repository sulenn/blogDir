# sulenn个人博客

**[个人博客](https://sulenn.github.io/)**

**所用框架：** **[hexo](https://hexo.io/zh-cn/)** ，**主题：** **[Clean Blog](http://www.codeblocq.com/assets/projects/hexo-theme-clean-blog/)**，**作者：** **[Klughertz Jonathan](https://github.com/klugjo)**

## 1. 安装Hexo

检查电脑中是否已安装下列应用程序：

- Node.js
- Git

如果已安装，则运行下列指令使用 npm 安装 Hexo

```shell
npm install -g hexo-cli
```

如果未安装，请参考 [安装](https://hexo.io/zh-cn/docs/)

## 2. 建站

分别执行下列命令，在指定文件夹中新建所需要的文件

```shell
hexo init <folder>
cd <folder>
npm install
```

完成后，指定文件夹的目录如下：

```shell
.
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
```

## 3. 服务器

安装 hexo-server

```shell
npm install hexo-server --save
```

启动服务器

```shell
hexo server
```

然后，可以通过 `http://localhost:4000` 查看你的网站

## 4. 部署

安装 `hexo-deployer-git`

```shell
npm install hexo-deployer-git --save
```

这里需要在github上申请一个 `repository` ，仓库名为 `ID` + `github.io`。如我个人的仓库名 [sulenn.github.io](https://github.com/sulenn/sulenn.github.io)

修改配置文件 `_config.yml` 内容

```shell
deploy:
  type: git
  repo: <repository url> #https://bitbucket.org/JohnSmith/johnsmith.bitbucket.io
  branch: master #published
```

此处的 `repository url` 需修改为上面所创建的仓库地址

分别执行以下指令，部署项目至github

```shell
hexo clean
hexo deploy
```

成功部署之后，不出意外的话，现在就可以通过仓库名访问博客了。

## 5. 更换博客theme

开篇介绍到本博客用的主题为： **[Clean Blog](http://www.codeblocq.com/assets/projects/hexo-theme-clean-blog/)**

先用 `git` 指令将主题文件 `clone` 至本地

```shell
git clone https://github.com/klugjo/hexo-theme-clean-blog.git themes/clean-blog
```

修改配置文件 `_config.yml` 内容

```shell
# Extensions
## Plugins: http://hexo.io/plugins/
## Themes: http://hexo.io/themes/
theme: clean-blog
```

执行和第4部分相同的指令，部署项目至github

```shell
hexo clean
hexo deploy
```

此时可发现网页主题已经生效。

除了重新部署至github以外也可以在本地启动服务器进行测试

## 6. 总结

通过以上步骤，我们可以轻松搭建个人博客。如果想熟练使用，需要私下多花费一些功夫。