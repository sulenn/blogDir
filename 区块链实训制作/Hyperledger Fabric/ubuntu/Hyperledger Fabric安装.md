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

## load images

```shell
cd /opt/hyperledger-fabric-images
docker load -i fabric-baseos.tar
docker load -i fabric-ccenv.tar
docker load -i fabric-orderer.tar
docker load -i fabric-peer.tar
docker load -i fabric-tools.tar
docker tag hyperledger/fabric-ccenv:1.4.0 hyperledger/fabric-ccenv:latest
```

## 提供可执行权限

```shell
chmod -R 755 /opt/go/src/github.com/hyperledger/fabric/scripts/fabric-samples/bin/

chmod -R 755 /opt/go/src/github.com/hyperledger/fabric/scripts/fabric-samples/first-network/scripts/
```

## 设置 COMPOSE_HTTP_TIMEOUT

```shell
echo "export COMPOSE_HTTP_TIMEOUT=500" >> /etc/profile
echo "export DOCKER_CLIENT_TIMEOUT=500" >> /etc/profile

source /etc/profile
```

## 构建 fabric 网络

```shell
cd /opt/go/src/github.com/hyperledger/fabric/scripts/fabric-samples/first-network/

vim byfn.sh
# 在命令行模式下输入：
set number
# 将第 491 行修改为
IMAGETAG="1.4.0"

bash ./byfn.sh up
```

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbmwjpi45rj317302at8z.jpg)
