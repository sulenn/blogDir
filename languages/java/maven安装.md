# maven安装

## 1. ubuntu

**Maven 下载地址**：[http://maven.apache.org/download.cgi](http://maven.apache.org/download.cgi)

![2](http://ww1.sinaimg.cn/large/006alGmrly1g29xcamg5pj30ha07ggn1.jpg)

**解压**:

```shell
tar -xvf  apache-maven-3.3.9-bin.tar.gz
sudo mv -f apache-maven-3.3.9 /usr/local/
```

编辑 `/etc/profile` 文件 `sudo vim /etc/profile`，在文件末尾添加如下代码：

```shell
export MAVEN_HOME=/usr/local/apache-maven-3.3.9
export PATH=${PATH}:${MAVEN_HOME}/bin
```

是环境变量生效，并查看版本，确定是否安装成功：

```shell
source /etc/profile
mvn -v
```

**参考**:[http://www.runoob.com/maven/maven-setup.html](http://www.runoob.com/maven/maven-setup.html)