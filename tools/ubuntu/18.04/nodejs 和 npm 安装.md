# nodejs 和 npm 安装

##　1. 安装Node.js

Ubuntu 18.04在其默认存储库中包含一个版本的Node.js，可用于在多个系统间提供一致的体验。 在撰写本文时，存储库中的版本是8.10.0。 这不会是最新的版本，但它应该稳定且足以快速实验该语言。

要获得此版本，您可以使用apt软件包管理器。 键入以下内容刷新本地包索引：

`sudo apt update`

从存储库安装Node.js：

`sudo apt install nodejs`

需要额外安装npm。你可以通过输入以下命令来完成

`sudo apt install npm`

## 2. 升级node

利用n来管理版本

`sudo npm install -g n`

n来下载node版本

`sudo n lts`

查看node版本

`node -v`

## 3. 升级npm

`sudo npm i -g npm`

**参考：**[ubuntu18.04下安装node](https://www.cnblogs.com/guanine/p/9392411.html)