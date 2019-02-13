# ubuntu18.04安装wine QQ

## 1. 安装wine

```shell
wget -nc https://dl.winehq.org/wine-builds/Release.key
sudo apt-key add Release.key
sudo apt-add-repository https://dl.winehq.org/wine-builds/ubuntu/
sudo apt-get update
sudo apt-get install winehq-devel
```

## 2. 安装wineQQ

这里我们直接下载压缩包，解压之后就直接可以使用了。

下载地址：[https://pan.baidu.com/s/1KAP1h4qVDBi_LY4lBrfCYQ](https://pan.baidu.com/s/1KAP1h4qVDBi_LY4lBrfCYQ) 提取密码：4854

下载完成之后，进入到下载目录，打开命令行终端，将其解压到我们的用户主目录（解压出来的是3个隐藏目录和说明文件）：

tar xvf wineQQ8.9_19990.tar.xz -C ~/

安装完成之后在，在桌面上是看不到QQ图标的，这和windows安装软件是不一样的：

windows键，在搜索栏输入qq，就可以看到我们的软件图标了，点击图标就可以启动了。

![2](http://ww1.sinaimg.cn/large/006alGmrgy1g054dtvd0sj311y0lcn7f.jpg)

**参考**：[ubuntu18.04安装wine QQ](https://blog.csdn.net/zz_ly/article/details/83024449)