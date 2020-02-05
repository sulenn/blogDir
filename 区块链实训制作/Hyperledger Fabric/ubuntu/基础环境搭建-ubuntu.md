# 基础环境搭建

`运行镜像或者进入容器：docker exec -it --privileged containerId /bin/bash`

`docker run -it --privileged containerId /bin/bash`

- 安装 git：`apt-get -y install git`

  查看版本：`git --version` (git version 2.17.1)

- 安装 cURL：`apt-get -y install curl`

  查看版本：`curl--version`

  curl 7.58.0 (x86_64-pc-linux-gnu) libcurl/7.58.0 OpenSSL/1.1.1 zlib/1.2.11 libidn2/2.0.4 libpsl/0.19.1 (+libidn2/2.0.4) nghttp2/1.30.0 librtmp/2.3
    Release-Date: 2018-01-24
    Protocols: dict file ftp ftps gopher http https imap imaps ldap ldaps pop3 pop3s rtmp rtsp smb smbs smtp smtps telnet tftp
    Features: AsynchDNS IDN IPv6 Largefile GSS-API Kerberos SPNEGO NTLM NTLM_WB SSL libz TLS-SRP HTTP2 UnixSockets HTTPS-proxy PSL

- 安装 make：`apt-get -y install make`

  查看版本：`make --version`

  GNU Make 4.1
    Built for x86_64-pc-linux-gnu
    Copyright (C) 1988-2014 Free Software Foundation, Inc.
    License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
    This is free software: you are free to change and redistribute it.
    There is NO WARRANTY, to the extent permitted by law.

- 安装 gcc：`apt-get -y install gcc`

  查看版本：`gcc --version`

  gcc (Ubuntu 7.4.0-1ubuntu1~18.04.1) 7.4.0
    Copyright (C) 2017 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

- 安装 wget：`apt-get -y install wget`

  查看版本：`wget --version`

  GNU Wget 1.19.4 built on linux-gnu.

    -cares +digest -gpgme +https +ipv6 +iri +large-file -metalink +nls
    +ntlm +opie +psl +ssl/openssl

    Wgetrc:
        /etc/wgetrc (system)
    Locale:
        /usr/share/locale
    Compile:
        gcc -DHAVE_CONFIG_H -DSYSTEM_WGETRC="/etc/wgetrc"
        -DLOCALEDIR="/usr/share/locale" -I. -I../../src -I../lib
        -I../../lib -Wdate-time -D_FORTIFY_SOURCE=2 -DHAVE_LIBSSL -DNDEBUG
        -g -O2 -fdebug-prefix-map=/build/wget-Xb5Z7Y/wget-1.19.4=.
        -fstack-protector-strong -Wformat -Werror=format-security
        -DNO_SSLv2 -D_FILE_OFFSET_BITS=64 -g -Wall
    Link:
        gcc -DHAVE_LIBSSL -DNDEBUG -g -O2
        -fdebug-prefix-map=/build/wget-Xb5Z7Y/wget-1.19.4=.
        -fstack-protector-strong -Wformat -Werror=format-security
        -DNO_SSLv2 -D_FILE_OFFSET_BITS=64 -g -Wall -Wl,-Bsymbolic-functions
        -Wl,-z,relro -Wl,-z,now -lpcre -luuid -lidn2 -lssl -lcrypto -lpsl
        ftp-opie.o openssl.o http-ntlm.o ../lib/libgnu.a

    Copyright (C) 2015 Free Software Foundation, Inc.
    License GPLv3+: GNU GPL version 3 or later
    <http://www.gnu.org/licenses/gpl.html>.
    This is free software: you are free to change and redistribute it.
    There is NO WARRANTY, to the extent permitted by law.

    Originally written by Hrvoje Niksic <hniksic@xemacs.org>.
    Please send bug reports and questions to <bug-wget@gnu.org>.

- 安装 vim：`apt-get -y install vim`

- 安装 docker：

  可能出现的问题：
  1. System has not been booted with systemd as init system (PID 1). Can't operate
    执行 `systemctl start docker` 出错，应该和 centos 中的错误一样

  `apt-get -y install apt-transport-https`

  `apt-get -y install software-properties-common`

  `curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -`

  `add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"`

  `apt-get update`

  `apt-get -y install docker-ce=18.06.3~ce~3-0~ubuntu`

  `service docker start`

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

  `service docker restart`

  **参考**：
  
  - [干净删除已装的 docker](https://blog.csdn.net/weixin_41282397/article/details/86524297)

  - [Ubuntu安装与卸载docker](https://blog.csdn.net/longzhoufeng/article/details/82421322)

- 安装 python-pip：

  `apt-get -y install python-pip`

- 更新 pip：`pip install --upgrade pip`

  查看版本：`pip --version`

  pip 20.0.2 from /usr/lib/python2.7/site-packages/pip (python 2.7)

- 安装 docker-compose：

  `pip install docker-compose`

  查看版本：`docker-compose --version`

  docker-compose version 1.25.4, build unknown

- 安装 golang：

  1. 下载安装包：

     `cd /opt`

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

     `cd /opt`

     `mkdir -p go`

     `echo "export GOPATH=/opt/go" >> /etc/profile`

     `source /etc/profile`
