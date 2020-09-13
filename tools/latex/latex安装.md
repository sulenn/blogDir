[toc]

# windows 安装

## 1. texlive 下载并安装

**下载地址**：[https://mirrors.tuna.tsinghua.edu.cn/CTAN/systems/texlive/Images/](https://mirrors.tuna.tsinghua.edu.cn/CTAN/systems/texlive/Images/)

![2](http://ww1.sinaimg.cn/large/006alGmrly1g57w5vupm6j31gb0mjwia.jpg)

下载完之后解压，点击图中的程序，更换安装地址之后即可，安装时间可能需要一个多小时

![2](http://ww1.sinaimg.cn/large/006alGmrly1g57wn993zrj30vu0n077g.jpg)

`cmd` 下检查安装是否成功

![2](http://ww1.sinaimg.cn/large/006alGmrly1g57woxh88qj31210jd0w0.jpg)

## 2. texstudio 安装

从阿睿那儿拷过来，点击安装包，更换地址即可安装

![2](http://ww1.sinaimg.cn/large/006alGmrly1g57wqhkhz3j307x0680t8.jpg)

**参考**：

- [TeX Live 2018安装流程](https://zhuanlan.zhihu.com/p/36240727)

- [windows 下TexLive2018的安装和配置【转载】](https://blog.csdn.net/qq_38386316/article/details/80272396)

# ubuntu 18 安装

**执行命令**：

```shell
# 安装LaTeX发行版，TeX Live
sudo apt-get install texlive-full

# 安装图形化界面 texstudio
sudo apt-get install texstudio
```

**安装成功后可能缺少很多字体，可以从 windows 中将所有的字体粘贴至 ubuntu。ubuntu 安装字体的步骤如下：**

```shell
# 新建文件夹
bash sudo mkdir /usr/share/fonts/winfonts

# 复制字体至该文件夹
sudp cp ../.. /usr/share/fonts/winfonts

# 进入该文件夹，执行
cd /usr/share/fonts/winfonts
sudo mkfontscale
sudo mkfontdir

# 刷新字体缓存
sudo fc-cache -fv

# 查看字体是否安装，如宋体
> fc-list :lang=zh | grep '宋体'

/usr/share/fonts/winfonts/simsun_0.ttc: 宋体,SimSun:style=Regular
/usr/share/fonts/winfonts/simsun.ttc: 宋体,SimSun:style=常规,Regular
/usr/share/fonts/winfonts/simsun_0.ttc: 新宋体,NSimSun:style=Regular
/usr/share/fonts/winfonts/STSONG.TTF: 华文宋体,STSong:style=Regular
/usr/share/fonts/winfonts/simsun.ttc: 新宋体,NSimSun:style=常规,Regular

# 安装成功
```



**参考**：

- [Ubuntu18.04安装LaTeX并配置中文环境](https://blog.csdn.net/qq_41814939/article/details/82288145)
- [Linux 配置Latex 教程](https://zhuanlan.zhihu.com/p/55894177)