# python 环境搭建

## 1. windows 平台

### 1.1 编译器安装 - anaconda

anaconda 下载网址：[https://www.anaconda.com/distribution/#download-section](https://www.anaconda.com/distribution/#download-section)

默认安装，注意安装目录

- 安装好之后需要添加环境变量，当前环境变量为：

  *anaconda安装路径（为了Python检查正常）*：前面安装时路径一定要记清楚，之前已提醒复制，我的是D:\Python\install\Anaconda3
  *安装路径\Scripts（为了conda检查正常）*：只需在上述路径中找到Scripts，然后复制路径即可，我的路径是D:\Python\install\Anaconda3\Scripts
  *另一个路径（加不加不知道有什么区别，不影响后边的检查）*：安装路径\Library\bin，如我的是D:\Python\install\Anaconda3\Library\bin

  **上述三个环境变量都是通过**：此电脑—右键—高级系统设置—环境变量—系统变量—双击path—新建这两个变量即可。

- `shell` 中验证是否安装成功：

  `python --version`、`conda --version`

==如果出现添加环境变量之后，报错“conda 不是内部或外部命令”，则可以参考：[Window安装Anaconda后，conda不是内部或者外部命令](https://blog.csdn.net/u011361880/article/details/75294226)、[http://www.manongjc.com/article/27716.html](http://www.manongjc.com/article/27716.html)==

*参考*：[anaconda的安装与环境配置](https://www.jianshu.com/p/d3a5ec1d9a08)

### 1.2 集成开发工具 PyCharm

默认安装，注意安装路径即可

---

## 2. ubuntu 平台

### 2.1 编译器安装 - anaconda

`.sh` 文件默认安装即可

默认安装目录为 `/home/qiubing/anaconda3`

*添加环境变量，手动刷新加载即可使用*：export export PATH=/home/qiubing/anaconda3/bin:$PATH

**参考**：

- [Linux(Ubuntu 18.04)上安装Anaconda步骤详解](https://www.jb51.net/article/149932.htm)

- [Ubuntu 环境下安装 Anaconda](https://www.jianshu.com/p/895bcd4430c9)

### 2.2 集成开发工具 PyCharm

默认安装，和其它类似开发工具一样安装