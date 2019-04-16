# python 环境搭建

## 1. windows 平台

### 1.1 编译器安装 - anaconda

anaconda 下载网址：[https://www.anaconda.com/distribution/#download-section](https://www.anaconda.com/distribution/#download-section)

默认安装，注意安装目录

安装好之后需要添加两个环境变量，当前环境变量为：

`D:\Anaconda\Scripts`、`D:\Anaconda`

`shell` 中验证是否安装成功：

`python --version`、`conda --version`

==如果出现添加环境变量之后，报错“conda 不是内部或外部命令”，则可以参考：[Window安装Anaconda后，conda不是内部或者外部命令](https://blog.csdn.net/u011361880/article/details/75294226)、[http://www.manongjc.com/article/27716.html](http://www.manongjc.com/article/27716.html)==

### 1.2 集成开发工具 PyCharm

默认安装，注意安装路径即可

---

## 2. ubuntu 平台

### 2.1 编译器安装 - anaconda

`.sh` 文件默认安装即可

默认安装目录为 `/home/qiubing/anaconda3`

最后，在 shell 中键入 python 还是 Ubuntu 自带的 python 版本，这是因为 .bashrc 更新还没有生效，在 shell 中输入：

`source ~/.bashrc`

**参考**：[Ubuntu 环境下安装 Anaconda](https://www.jianshu.com/p/895bcd4430c9)

### 2.2 集成开发工具 PyCharm

默认安装，何其它类似开发工具一样安装