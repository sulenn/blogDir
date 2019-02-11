# zsh与oh-my-zsh

## 1. ubuntu中默认安装了哪些shell

`cat /etc/shells`

## 2.当前正在运行的是那个版本的shell

`echo $SHELL/bin/bash`

## 正式安装zsh、git和wget

- 获取并自动按照oh-my-zsh：
    `sudo apt-get install zsh git wget`
    `wget --no-check-certificate https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh`

- 替换bash为zsh：
    `chsh -s /bin/zsh`

- 最后重启电脑：
    `sudo reboot`

**参考至：** [https://www.jianshu.com/p/546effd99c35](https://www.jianshu.com/p/546effd99c35)