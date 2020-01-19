# 环境配置和 IDE 安装

## 1. ubuntu

### 安装 Clion

**下载地址**：[https://www.jetbrains.com/clion/download/#section=linux](https://www.jetbrains.com/clion/download/#section=linux)

### 安装C++开发环境

```shell
#安装gcc
sudo apt-get install gcc
#安装g++
sudo apt-get install g++
#安装cmake
sudo apt-get install cmake
```

**参考**：[Ubuntu搭建C++开发环境](Ubuntu搭建C++开发环境)

---

## 2. windows

## 2.1 MinGW-w64 安装

==请参考 `MinGW-w64安装教程` 安装==

**下载地址**：[http://mingw-w64.org/doku.php](http://mingw-w64.org/doku.php)

![1SxDIA.png](https://s2.ax1x.com/2020/01/18/1SxDIA.png)

下滑，选择

![1Sx6RP.png](https://s2.ax1x.com/2020/01/18/1Sx6RP.png)

点击，进入

![1SxRsS.png](https://s2.ax1x.com/2020/01/18/1SxRsS.png)

下滑，下载

![1SxTGq.png](https://s2.ax1x.com/2020/01/18/1SxTGq.png)

**安装**：

点击 `MinGW-W64-install.exe` 安装包，如果出现错误 ==cannot download repository.txt== 可以挂梯子下载。

剩下的安装步骤参考链接

**添加环境变量**：

![1Sz8eg.png](https://s2.ax1x.com/2020/01/18/1Sz8eg.png)

**测试**：

![1SzWSx.png](https://s2.ax1x.com/2020/01/18/1SzWSx.png)

安装完了之后如果使用不了。就重启一下电脑，加载编译环境

**参考**：

- [MinGW-w64安装教程](https://www.jianshu.com/p/d66c2f2e3537)

- [安装MinGW-W64提示cannot download repository.txt-解决方法](https://blog.csdn.net/m0_38051388/article/details/87000845)

## 2.2 MinGW 安装

==MinGW 只能编译生成32位可执行程序。不要装==

**安装MinGW**：

`下载地址`：[https://sourceforge.net/projects/mingw/](https://sourceforge.net/projects/mingw/) 或者 [http://www.mingw.org/](http://www.mingw.org/)

获得 `mingw-get-setup.exe`

![1SqhVI.png](https://s2.ax1x.com/2020/01/18/1SqhVI.png)

安装 `mingw-get-setup.exe`

![s](https://images2015.cnblogs.com/blog/1049916/201701/1049916-20170106215333706-1859177955.png)

运行 `MinGW Installer`，界面如下：

![1SLpGT.png](https://s2.ax1x.com/2020/01/18/1SLpGT.png)

将Basic Setup中的Package都打上勾（实际上并不是所有的Package都有用，无脑操作的话就都勾上）。

选择的方法：点击对应的Package，选择Mark for Installation。

![2](https://images2015.cnblogs.com/blog/1049916/201701/1049916-20170106220103003-1462716428.png)

点击Installation->Apply Changes.如下图示.

![2](https://images2015.cnblogs.com/blog/1049916/201701/1049916-20170106220448737-1858884074.png)

**配置环境变量**：

![1SLFsJ.png](https://s2.ax1x.com/2020/01/18/1SLFsJ.png)

**测试**：

cmd 中输入 `gcc -v`，如下：

![1SLuRO.png](https://s2.ax1x.com/2020/01/18/1SLuRO.png)

**参考**：

- [windows下安装MinGW及C++的环境配置](https://blog.csdn.net/u013171283/article/details/80898442)

- [MinGW安装和使用](https://www.jianshu.com/p/e9ff7b654c4a)

---
