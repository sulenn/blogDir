# Hyperledger Fabric安装

1. 下载 fabric 源码和镜像

   ```shell
   mkdir $GOPATH/src/github.com/hyperledger
   
   cd $GOPATH/src/github.com/hyperledger
   
   git clone https://github.com/hyperledger/fabric.git
   
   切换版本到1.4
   cd fabric
   git checkout -b release-1.4 origin/release-1.4
   
   cd script
   
   下载镜像
   $ ./bootstrap.sh
   
   ```

   