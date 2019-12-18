# 服务器+nginx+hexo搭建

## hexo 搭建

参考`个人博客搭建.md`

**添加hexo-admin 后台管理**：

当你每次写文章就要重新生成一次静态页面，并且重新启动hexo，这样当然是很繁琐的。
hexo-admin 可以解决这个问题，你可以在hexo-admin新增和编辑你的文章，完全不需要其他的繁琐步骤。

```node
npm i hexo-admin --save
hexo server -d
```

打开 `http://ip:4000/admin/` 进入后台管理页面

---

## 服务器配置

### 1. 创建用户并配置其仓库

```shell
useradd git
passwd git // 设置密码(chinaGIT2734,)
su git // 这步很重要，不切换用户后面会很麻烦
cd /home/git/
mkdir -p projects/blog // 项目存在的真实目录
mkdir repos && cd repos
git init --bare blog.git // 创建一个裸露的仓库
cd blog.git/hooks
vi post-receive // 创建 hook 钩子函数，输入了内容如下
```

```shell
#!/bin/sh
git --work-tree=/home/git/projects/blog --git-dir=/home/git/repos/blog.git checkout -f
```

添加完毕后修改权限

```shell
chmod +x post-receive
exit // 退出到 root 登录
chown -R git:git /home/git/repos/blog.git // 添加权限
```

### 2. 测试`git仓库`是否可用，本地pc另找空白文件夹

```shell
git clone git@server_ip:/home/git/repos/blog.git
```

如果能把空仓库拉下来，就说明 git 仓库搭建成功了

---

## nginx 安装

参考`/tool/nginx/安装`

**修改配置文件**：

```shell
vim /etc/nginx/nginx.conf
```

![image.png](https://ws1.sinaimg.cn/large/006alGmrgy1ga0q2hqu1cj31090k9wgn.jpg)

![image.png](https://ws1.sinaimg.cn/large/006alGmrgy1ga0q3grcrfj30y40fvq47.jpg)

---

## 发布

### 配置`_config.yml`文件

1. 编辑 _config.yml 的 deploy 属性

   ![image.png](https://ws1.sinaimg.cn/large/006alGmrgy1ga0vi15l52j30vt09tmye.jpg)

2. 在 package.json 中添加 npm 脚本

   ![image.png](https://ws1.sinaimg.cn/large/006alGmrgy1ga0vktksm1j30w90kitb6.jpg)

3. 这下在本地调试就用`npm start`，调试好了就通过`npm run deploy`上传到服务器

**参考**：

1. [带你跳过各种坑，一次性把 Hexo 博客部署到自己的服务器](https://blog.csdn.net/qq_35561857/article/details/81590953)
2. [从零搭建Hexo博客并部署阿里云服务器（奶妈级教学）](https://blog.csdn.net/NoCortY/article/details/99631249)
3. [在个人服务器利用hexo搭建博客](https://blog.csdn.net/weixin_34329187/article/details/88669732)