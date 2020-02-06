# Hyperledger Fabric安装

1. 下载 fabric 源码和镜像

   ```shell
   mkdir -p $GOPATH/src/github.com/hyperledger
   
   cd $GOPATH/src/github.com/hyperledger
   
   git clone https://github.com/hyperledger/fabric.git (如果下载很慢的话可以从本机 copy. 命令：docker cp C:\Users\qiubing\Desktop\fabric 9a09ac1450b0:/root/go/src/github.com/hyperledger)
   
   切换版本到1.4
   cd fabric
   git checkout -b release-1.4
   
   cd scripts
   
   下载镜像
   ./bootstrap.sh 1.4.0
   ```
   
![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbi6fx9eeuj30x40lcn1s.jpg)
   
2. 测试 byfn

   ```shell
   cd fabric-samples
   cd first-network/
   echo "export PATH=/opt/go/src/github.com/hyperledger/fabric/scripts/fabric-samples/bin:$PATH" >> /etc/profile
   source /etc/profile
   
   运行 byfn.sh
   
   ./byfn.sh up
   
   关闭 byfn.sh
   
   ./byfn.sh down
   ```

   
