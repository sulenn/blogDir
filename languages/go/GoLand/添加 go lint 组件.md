[toc]

# 添加 go lint 组件

## goreturns 组件可以替代 goimports 和 go fmt

- **安装（成功后，GOPATH/bin 中会有 goreturns 的可执行文件）**：go get -u github.com/sqs/goreturns

- **手动添加 goreturns 可执行文件**：![image.png](http://ww1.sinaimg.cn/large/006alGmrgy1ggxb83d99qj319j0k841u.jpg)

- **填信息**：![image.png](http://ww1.sinaimg.cn/large/006alGmrgy1ggxb9pe2iej30rv0yjwhy.jpg)



## 添加 go fmt、goimports、gometalinter

- 下载 goimports：

  ```shell
  go get golang.org/x/tools/cmd/goimports
  ```

  

- **File -> Settings -> Tools -> File Watchers**

  ![image.png](http://ww1.sinaimg.cn/large/006alGmrgy1ggkjtm50rwj30sx0cd436.jpg)

- 点`2`处的加号、里面有go的三个工具，都添加上。默认点击 OK 即可

  ![image.png](http://ww1.sinaimg.cn/large/006alGmrgy1ggkjukpq9uj30bg0c0q6v.jpg)

- 点击 apply、OK

  ![image.png](http://ww1.sinaimg.cn/large/006alGmrgy1ggmv8qccgcj30sw0a6js0.jpg)
  
  ![image.png](http://ww1.sinaimg.cn/large/006alGmrgy1ggkjw45th4j30t40k4afg.jpg)

**参考**：[Goland安装Goimports工具](https://www.jianshu.com/p/020f94f0693e)

