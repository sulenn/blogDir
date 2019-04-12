# java安装

==eclipse 安装：[https://www.cnblogs.com/zyrblog/p/8548270.html](https://www.cnblogs.com/zyrblog/p/8548270.html)==

## 1. 通过 apt-get 安装

### 1.1 添加ppa

```shell
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
```

### 1.2 安装jdk

```shell
sudo apt-get install oracle-java8-installer
```

### 1.3 验证安装是否成功

```shell
java -version
```

**参考**：[Ubuntu 18.04安装JDK并配置环境变量](https://blog.csdn.net/pxmxx/article/details/80106239)

---

## 2. 通过安装包下载安装

### 2.1 java7 下载

 下载地址：[http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u80-oth-JPR](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u80-oth-JPR)

![2](http://ww1.sinaimg.cn/large/006alGmrly1g1yi1ps4epj30jh094ab6.jpg)

![2](http://ww1.sinaimg.cn/large/006alGmrly1g1yi2cjbprj30j30g241h.jpg)

在下载之前必须先点击 `Accept License Agreement`  ，不然下载不了。

点击 Downloade 下面的链接开始下载，这时可能会跳转到登录页面，之前注册过Oracle账号，直接登录，没有注册过的需要先完成注册，然后登录邮箱验证之后返回登录。登录成功之后才会开始下载。

**参考**：[官网下载JDK1.7的方法和步骤](https://blog.csdn.net/liu59412/article/details/82353392)

### 2.2 java7 安装

1. 解压

    ```shell
    sudo tar -C /usr/local -xzf jdk-7u80-linux-x64.tar.gz
    ```

2. 添加环境变量，修改配置文件

    执行：`sudo vim /etc/profile`

    然后添加：

    ```shell
    export JAVA_HOME=/usr/local/jdk1.7.0_80
    export CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH
    export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH
    export JRE_HOME=$JAVA_HOME/jre
    ```

3. 激活生效

    `source /etc/profile`

4. 查看是否配置成功

    `java -version`
    ![2](http://ww1.sinaimg.cn/large/006alGmrly1g1yio4m895j30gk01zt8v.jpg)

    `javac -version`
    ![2](http://ww1.sinaimg.cn/large/006alGmrly1g1yiojw706j3063010glh.jpg)

**参考**：[ubuntu18.04 安装java](https://blog.csdn.net/sangewuxie/article/details/80958611)