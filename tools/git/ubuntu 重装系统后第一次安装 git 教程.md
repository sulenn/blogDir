# ubuntu 重装系统后第一次安装 git 教程

- 安装：`sudo apt-get install git`

- git配置：`git config --global user.name "xxx"` 和 `git config --global user.email "你的邮箱地址"`

- 创建公钥(~/.ssh/)：`ssh-keygen -C 'you email address@gmail.com' -t rsa`

- 上传公钥至 github：

    ![2](http://ww1.sinaimg.cn/large/006alGmrgy1g1bbowfhyfj30is0b0q40.jpg)

- 删除 blogDir 中已有的 remote：`git branch remove origin`

- 添加新的 remote ：`git remote add origin git@github.com:sulenn/blogDir.git`

- push 一次测试：`git push origin master`

- 设置远程跟踪：`git branch --set-upstream-to=origin/master master`

- 添加新的修改之后，用 `git status` 测试是否和远程进行了比较，然后再 push 测试一下。

- over。。。

**参考**：[https://www.cnblogs.com/lxm20145215----/p/5905765.html](https://www.cnblogs.com/lxm20145215----/p/5905765.html)