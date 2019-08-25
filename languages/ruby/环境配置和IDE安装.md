# 环境配置和IDE安装

## 1. ruby 、 rails 环境配置

### 1.1 安装 RVM

```shell
gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
curl -sSL https://get.rvm.io | bash -s stable
```

期间可能会问你 sudo 管理员密码，以及自动通过 Homebrew 安装依赖包，等待一段时间后就可以成功安装好 RVM。

然后，载入 RVM 环境（新开 Termal 就不用这么做了，会自动重新载入的）

`source ~/.rvm/scripts/rvm`

修改 RVM 下载 Ruby 的源，到 Ruby China 的镜像:

`echo "ruby_url=https://cache.ruby-china.com/pub/ruby" > ~/.rvm/user/db`

检查一下是否安装正确

`rvm -v`

### 1.2 用 RVM 安装 Ruby 环境

`rvm requirements`
`rvm install 2.3.3`

同样继续等待漫长的下载，编译过程，完成以后，Ruby, Ruby Gems 就安装好了。

### 1.3 设置 Ruby 版本

RVM 装好以后，需要执行下面的命令将指定版本的 Ruby 设置为系统默认版本

`rvm use 2.3.3 --default`

同样，也可以用其他版本号，前提是你有用 `rvm install` 安装过那个版本

这个时候你可以测试是否正确

`ruby -v`
`gem -v`
`gem sources --add https://gems.ruby-china.com/ --remove https://rubygems.org/`
`gem install bundler`

### 1.4 安装 Rails 环境

上面 3 个步骤过后，Ruby 环境就安装好了，接下来安装 Rails

`gem install rails -v 3.2.16`

然后测试安装是否正确

`rails -v`

**参考**：[如何快速正确的安装 Ruby, Rails 运行环境](https://ruby-china.org/wiki/install_ruby_guide)

---

## 2. 安装 rubymine

**下载地址**：[https://www.jetbrains.com/ruby/](https://www.jetbrains.com/ruby/)

下载完之后，解压缩，移动到 `home\qiubing\qiubing` 目录下。然后参考 linux/ubuntu/18.04 中添加 .sh 脚本
