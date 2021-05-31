[toc]
# Typora 安装

## 命令行安装

*如果网络不太好的话，安装会非常慢*

```shell
for Linux
# or run:
# sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys BA300B7755AFCFAE
wget -qO - https://typora.io/linux/public-key.asc | sudo apt-key add -
# add Typora's repository
sudo add-apt-repository 'deb https://typora.io/linux ./'
sudo apt-get update
# install typora
sudo apt-get install typora
```

## 二进制包安装

**下载**：https://typora.io/linux/Typora-linux-x64.tar.gz

*安装过程和 intellij IDEA 一致*

执行命令：sudo vim /usr/share/applications/typora.desktop

输入：

```shell
[Desktop Entry]
    Name=Typora
    Exec=/home/qiubing/qiubing/Typora-linux-x64/Typora
    Comment=Typora
    Icon=/home/qiubing/qiubing/Typora-linux-x64/resources/app/asserts/icon/icon_256x256.png
    Type=Application
    Terminal=false
    Encoding=UTF-8
```

**参考**：

- [typora for linux](https://typora.io/#linux)

- [二进制 Typora 在 Ubuntu (gnome) 上安装为应用程序](https://blog.csdn.net/qq_29757283/article/details/86800197)

## 主题和图床

- 配置图床：SM.MS，参考：[https://zhuanlan.zhihu.com/p/137310314](https://zhuanlan.zhihu.com/p/137310314)

- 主题使用：Barfi，暗黑风格

- 通用设置：![image-20200727130108232](https://i.loli.net/2020/07/27/8i4bTMIEWRFcLHw.png)
