# 基础环境搭建

运行镜像的命令：`docker run -d -it --privileged imageId /usr/sbin/init`

进行容器内部：`docker exec -it containerId /bin/bash`

`只有通过上两条命令才可以在安装 docker 后运行 systemctl start nginx 命令，不然会报错：Failed to get D-Bus connection: Operation not permitted。报错的原因是运行容器时使用的权限问题。解决方案参考：`[https://blog.csdn.net/weixin_42123737/article/details/87984996](https://blog.csdn.net/weixin_42123737/article/details/87984996)

`centos 才会有运行 systemctl start nginx 命令出错的问题。ubuntu 就不会有这个问题`

- 查看 centos 版本：cat /etc/redhat-release （docker pull 该 centos 的命令 `docker pull centos:7.6.1810`）

  CentOS Linux release 7.6.1810 (Core)

- 安装 git：`yum -y install git`

  查看版本：`git --version` (git version 1.8.3.1)

- 安装 cURL：`yum -y install curl`

  查看版本：`curl--version` 

  curl 7.29.0 (x86_64-redhat-linux-gnu) libcurl/7.29.0 NSS/3.44 zlib/1.2.7 libidn/1.28 libssh2/1.8.0
  Protocols: dict file ftp ftps gopher http https imap imaps ldap ldaps pop3 pop3s rtsp scp sftp smtp smtps telnet tftp 
  Features: AsynchDNS GSS-Negotiate IDN IPv6 Largefile NTLM NTLM_WB SSL libz unix-sockets

- 安装 ca-certificates：`yum -y install ca-certificates`

- 安装 make：`yum -y install make`

  查看版本：`make --version`

  GNU Make 3.82
  Built for x86_64-redhat-linux-gnu
  Copyright (C) 2010  Free Software Foundation, Inc.
  License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
  This is free software: you are free to change and redistribute it.
  There is NO WARRANTY, to the extent permitted by law.

- 安装 gcc：`yum -y install gcc`

  查看版本：`gcc --version`

  Copyright (C) 2015 Free Software Foundation, Inc.
  This is free software; see the source for copying conditions.  There is NO
  warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

- 安装 wget：`yum -y install wget`

  查看版本：`wget --version`

  GNU Wget 1.14 built on linux-gnu.

  +digest +https +ipv6 +iri +large-file +nls +ntlm +opie +ssl/openssl

  Wgetrc:
      /etc/wgetrc (system)
  Locale: /usr/share/locale
  Compile: gcc -DHAVE_CONFIG_H -DSYSTEM_WGETRC="/etc/wgetrc"
      -DLOCALEDIR="/usr/share/locale" -I. -I../lib -I../lib -O2 -g -pipe
      -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong
      --param=ssp-buffer-size=4 -grecord-gcc-switches -m64 -mtune=generic
  Link: gcc -O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions
      -fstack-protector-strong --param=ssp-buffer-size=4
      -grecord-gcc-switches -m64 -mtune=generic -lssl -lcrypto
      /usr/lib64/libssl.so /usr/lib64/libcrypto.so /usr/lib64/libz.so
      -ldl -lz -lz -lidn -luuid -lpcre ftp-opie.o openssl.o http-ntlm.o
      ../lib/libgnu.a

  Copyright (C) 2011 Free Software Foundation, Inc.
  License GPLv3+: GNU GPL version 3 or later
  <http://www.gnu.org/licenses/gpl.html>.
  This is free software: you are free to change and redistribute it.
  There is NO WARRANTY, to the extent permitted by law.

  Originally written by Hrvoje Niksic <hniksic@xemacs.org>.
  Please send bug reports and questions to <bug-wget@gnu.org>.

- 更新软件：`yum update -y`

- 安装 docker：

  可能出现的问题：

  1. 执行 systemctl start docker 出现 Failed to get D-Bus connection: Operation not permitted 错误

     解决参考：[https://blog.csdn.net/weixin_42123737/article/details/87984996](https://blog.csdn.net/weixin_42123737/article/details/87984996)

  `yum -y install yum-utils`

  `yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo`

  `yum makecache`

  `yum -y install docker-ce-18.06.3.ce`

  `systemctl start docker`

  `systemctl enable docker`

  查看版本：`docker version`

  ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbhsmh2su8j31740fiwg8.jpg)

  设置阿里云的镜像：

  `vi /etc/docker/daemon.json`

  加入配置：

  ```shell
  {
       "registry-mirrors": ["https://aj2rgad5.mirror.aliyuncs.com"]
  }
  ```

  重启docker：

  `systemctl daemon-reload`
  `systemctl restart docker.service`

  **参考**：[https://blog.csdn.net/u012140251/article/details/90445192](https://blog.csdn.net/u012140251/article/details/90445192)

- 安装 python-pip：

  可能会出现两个问题 (下面的命令已经融合了两种解决方案)：

  1. No package python-pip available.

     解决参考：[No package python-pip available. 解决方法](https://www.cnblogs.com/music-liang/p/11886258.html)

  2. Error: Package: python-pip-7.1.0-1.el6.noarch (epel)

     解决参考：[centos安装docker-compose](https://www.jianshu.com/p/b02e25c6004f)

  `rpm -ivh http://dl.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm`

  `yum clean all`

  `yum -y remove epel-release`

  `yum -y install epel-release --enablerepo=extras`

  `yum -y install python-pip`

- 更新 pip：`pip install --upgrade pip`

  查看版本：`pip --version`

  pip 20.0.2 from /usr/lib/python2.7/site-packages/pip (python 2.7)

- 安装 docker-compose：

  可能出现以下问题 (下面的命令已经融合了解决方案)：

  1. command 'gcc' failed with exit status 1

     解决参考：[command 'gcc' failed with exit status 1错误问题的解决办法](https://blog.csdn.net/enweitech/article/details/80728434)

  `yum -y install python-devel`

  `pip install docker-compose`

  查看版本：`docker-compose --version`

  docker-compose version 1.25.3, build unknow

- 安装 golang：

  1. 下载安装包：

     `cd ~`

     `wget https://dl.google.com/go/go1.11.1.linux-amd64.tar.gz`

     将 `go1.11.5.linux-amd64.tar.gz` 解压至 `/usr/local`

  2. 解压：`tar -C /usr/local -xzf go1.11.1.linux-amd64.tar.gz`

  3. 添加环境变量，修改配置文件

     ```shell
     echo "export PATH=$PATH:/usr/local/go/bin" >> /etc/profile
     ```

     运行如下命令，让修改在当前 bash 中生效，注意是当前，如果要一直生效需要重启电脑

     ```shell
     source /etc/profile
     ```

  4. 查看版本：`go version`

     `go version go1.11.1 linux/amd64`

- 安装 nodejs

  1. ​	安装 Node

     ```shell
     cd ~
     wget https://nodejs.org/dist/v8.16.0/node-v8.16.0-linux-x64.tar.xz
     xz -d node-v8.16.0-linux-x64.tar.xz
     tar xvf node-v8.16.0-linux-x64.tar
     mv node-v8.16.0-linux-x64 /usr/local/node
     ```

  2. 配置环境变量

     ```shell
     echo "export NODE_HOME=/usr/local/node" >> /etc/profile
     echo "export PATH=/usr/local/node/bin:$PATH" >> /etc/profile
     source /etc/profile
     ```

  3. 查看 node 版本：`node --version`

     v8.16.0

  4. 配置 GOPATH 路径

     `cd /root`

     `mkdir -p go`

     `echo "export GOPATH=/root/go" >> /etc/profile`

     `source /etc/profile`

     `mkdir -p $GOPATH/bin`

     `mkdir -p $GOPATH/src`

     `mkdir -p $GOPATH/pkg

     