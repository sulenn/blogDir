[toc]

# zsh与oh-my-zsh

## 1. ubuntu中默认安装了哪些shell

`cat /etc/shells`

## 2. 当前正在运行的是那个版本的shell

`echo $SHELL/bin/bash`

## 3. 正式安装zsh、git和wget

- 获取并自动按照oh-my-zsh：
    `sudo apt-get install zsh git wget`
    `wget --no-check-certificate https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh`

    **可能会出错，Failedtoconnecttoraw.githubusercontent.comport443，可以参考：[https://bbs.huaweicloud.com/blogs/143682](https://bbs.huaweicloud.com/blogs/143682) 解决**

- 替换bash为zsh：
    `chsh -s /bin/zsh`

- 最后重启电脑：
    `sudo reboot`

==注意：如果重启之后没有效果就手动删掉`/home/qiubing/.oh-my-zsh`，然后重装试试==

![2](http://ww1.sinaimg.cn/large/006alGmrgy1g1bngkbvfpj30ss0gjn2u.jpg)

## 4. 配置 zsh-autosuggestions

提供命令行自动提示功能

```shell
# 下载
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

**修改 .zshrc 配置文件， 添加 plugins=(zsh-autosuggestions)**：

![image.png](http://ww1.sinaimg.cn/large/006alGmrgy1ggbbdsisbgj30s30dkjvt.jpg)

启动一个新的终端即可查看到效果

**参考至**：

- [https://www.jianshu.com/p/546effd99c35](https://www.jianshu.com/p/546effd99c35)
- [Installation](https://github.com/zsh-users/zsh-autosuggestions/blob/master/INSTALL.md)

---

## 错误：`zsh: corrupt history file /home/qiubing/.zsh_history`

![2](http://ww1.sinaimg.cn/large/006alGmrly1g2ccj3g6erj30kr03ywf0.jpg)

**解决方法**：

```shell
cd ~
mv .zsh_history .zsh_history_bad
strings .zsh_history_bad > .zsh_history
fc -R .zsh_history
```

**参考**：[https://shapeshed.com/zsh-corrupt-history-file/](https://shapeshed.com/zsh-corrupt-history-file/)

## Mac 

接着下载两个命令行插件，一个是历史命令自动补全

```
git clone git:``//github``.com``/zsh-users/zsh-autosuggestions` `$ZSH_CUSTOM``/plugins/zsh-autosuggestions
```

一个是高亮显示

```
git clone git:``//github``.com``/zsh-users/zsh-syntax-highlighting` `$ZSH_CUSTOM``/plugins/zsh-syntax-highlighting
```

安装完改一下配置文件

```
vim ~/.zshrc<br>``#或者<br>gedit ~/.zshrc
```

把plugins中两个刚刚下载好的插件名字加进去，然后保存退出

```
plugins=(`` ``git`` ``zsh-autosuggestions`` ``zsh-syntax-highlighting``)
```

最后更新一下配置文件

```
source` `.zshrc
```

安装成功

1. https://blog.csdn.net/weixin_43847957/article/details/115395696

2. https://www.cnblogs.com/voyage1969/p/13717296.html