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

## educoder 平台测试 fabric 网络可能的出错情况

1. Error: could not assemble transaction, err proposal response was not successful, error code 500, msg timeout expired while starting chaincode mycc:1.0 for transaction

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gbnvd94oyjj311c0hggos.jpg)

    - [https://www.oschina.net/question/4099010_2304523](https://www.oschina.net/question/4099010_2304523)
        telnet测试一下，如果没问题的话，访问的时候，查看一下order节点的日志，docker logs -f -t --tail 100 orderer.example.com

    - [https://www.jianshu.com/p/248530bf31b8](https://www.jianshu.com/p/248530bf31b8)
        链码升级时报错，重启peer时发现启动不了，原来我链码中引用的一个config.go的文件，此文件有一个init函数会加载同目录下的一个yaml配置文件。而fabric 的链码打包安装到docker时，并不会加载非go的文件，因此，实例化时找这个文件找不到就panic报错了。由于fabric启动docker容器失败会自动删除docker容器，因此本应该能打印出来的错误被fabric给干掉了，因此出现题干的错误。谨记：所有的链码，必须是纯go文件写的，无任何非go依赖，如c文件，yml文件等，否则必然报错报错报错，还找不到原因！！！

    - [https://www.blockchainbrother.com/question/1216](https://www.blockchainbrother.com/question/1216)
        检查一下你执行链码实例化的时候，docker  peer orderer 是否已经全部启动，相关的域名是否已经和IP完成映射
