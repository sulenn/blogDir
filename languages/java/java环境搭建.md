# java环境搭建

## 1. windows 平台

### 1.1 jdk 安装

跟随教程默认安装就好了，直接装到 c 盘

**参考**:[Java 开发环境配置](http://www.runoob.com/java/java-environment-setup.html)

### 1.2 集成开发工具 idea

`.exe` 默认安装即可

---

## 2. ubuntu 平台

### 2.1 jdk 安装

`非常不建议用 apt 安装 jdk`

#### 2.1.1 通过 apt-get 安装

- 添加ppa

    ```shell
    sudo add-apt-repository ppa:webupd8team/java
    sudo apt-get update
    ```

- 安装jdk

    ```shell
    sudo apt-get install oracle-java8-installer
    ```

- 验证安装是否成功

    ```shell
    java -version
    ```

**参考**：[Ubuntu 18.04安装JDK并配置环境变量](https://blog.csdn.net/pxmxx/article/details/80106239)

---

#### 2.1.2 通过安装包下载安装

**jav11 下载**:

 下载地址：

1. [https://www.oracle.com/java/technologies/javase-downloads.html](https://www.oracle.com/java/technologies/javase-downloads.html) 究极慢，开了梯子都贼慢

2. [https://www.kagura.me/dev/20200424112618.html](https://www.kagura.me/dev/20200424112618.html) 国内的下载源

   ![image.png](http://ww1.sinaimg.cn/large/006alGmrgy1gg2byl5up7j30oy0hhtck.jpg)

**java11 安装**:

- 解压

    ```shell
    sudo tar -C ~/.jdks -xzf jdk-7u80-linux-x64.tar.gz
    ```

- 添加环境变量，修改配置文件

    执行：`sudo vim /etc/profile`

    然后添加：

    ```shell
    export JAVA_HOME=/home/qiubing/.jdks/jdk-11.0.7
    export CLASSPATH=/home/qiubing/.jdks/jdk-11.0.7/lib
    export PATH=$PATH:/home/qiubing/.jdks/jdk-11.0.7/bin
    ```
    
- 激活生效

    `source /etc/profile`

- 查看是否配置成功

    `java -version`
    ![2](http://ww1.sinaimg.cn/large/006alGmrly1g1yio4m895j30gk01zt8v.jpg)

    `javac -version`
    ![2](http://ww1.sinaimg.cn/large/006alGmrly1g1yiojw706j3063010glh.jpg)

**参考**：[ubuntu18.04 安装java](https://blog.csdn.net/sangewuxie/article/details/80958611)

### 2.2 集成开发工具

#### 2.2.1 idea

解压，然后一样的套路

#### 2.2.2 eclipse

==eclipse 安装：[https://www.cnblogs.com/zyrblog/p/8548270.html](https://www.cnblogs.com/zyrblog/p/8548270.html)==