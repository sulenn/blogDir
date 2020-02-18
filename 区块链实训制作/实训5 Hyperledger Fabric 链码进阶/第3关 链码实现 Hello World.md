[TOC]

---

####任务描述

本关任务：在 `Hyperledger Fabric` 网络中使用 `go` 编写链码实现 `Hello World`。

####相关知识

通过前面的实训和关卡我们已经接触了与链码相关的内容，本关卡我们根据已掌握的链码知识实现一个简单的链码应用。该应用需求较为简单：链码在实例化时向账本保存一个初始数据，`key` 为 `Hello`，`value` 为 `World`，然后用户发出查询请求，可以根据 `key` 查询到相应的 `value`。

#####链码开发

1. 创建文件夹。为了方便管理，进人 `fabric-samples/chaincode` 目录下并创建一个名为 `hello` 的文件夹。使用如下命令实现：

    ```shell
    # 切换路径
    cd /opt/go/src/github.com/hyperledger/fabric/scripts/fabric-samples/chaincode
    # 创建并进入 hello 文件夹
    mkdir hello && cd hello
    ```

2. 创建并编辑链码文件：

    ```shell
    vim hello.go
    ```

3. 导入链码依赖包：

    ```go
    package main

    import {
        "github. com/hyperledger/fabric/core/chaincode/shim"
        "github. com/hyperledger/ fabric/protos/peer"
        "fmt"
    }
    ```

4. 编写主函数：

    ```go
    func main() {
        err := shim.Start(new(HelloChaincode))
        if err != nil {
            fmt.Printf("链码启动失败 : %v", err)
        }
    }
    ```

5. 自定义结构体：

    ```go
    type HelloChaincode struct {}
    ```

6. 实现 `Chaincode` 接口。实现 `Chaincode` 接口必须重写 `Init` 与 `Invoke` 两个函数。

    - `Init` 函数：初始化数据状态，逻辑步骤如下。

        - 获取参数并判断参数长度是否为 2。

        - 调用 `PutState` 函数将状态写入账本中。

        - 如果有错误，则返回错误信息。

        - 输出提示信息。

        - 最后调用  `Success` 函数返回成功的状态。

        具体实现代码如下：

        ```go
        ∥实例化 / 升级链码时被自动调用
        // -c '("Args":["Hello", "World"]'
        func (t *HelloChaincode) Init(stub shim.ChaincodeStubInterface) peer.Response {
            fmt.Println("开始实例化链码......")
            // 获取参数
            // args := stub.GetStringArgs()
            _, args := stub.GetFunctionAndParameters()
            // 判断参数长度是否为 2 个
            if len(args) != 2 {
                return shim.Error("指定了错误的参数个数")
            }
            fmt.Println("保存数据......")

            // 通过调用 PutState 函数将数据保存在账本中
            err := stub.PutState(args[0], []byte(args[1]))
            if err != nil {
                return shim.Error("保存数据时发生错误......")
            }
            fmt.Println("实例化链码成功")
            return shim.Success(nil)
        }

    - `Invoke` 函数逻辑步骤如下。

        - 获取参数并判断长度是否为 1。

        - 利用第一个参数获取对应状态 GetState(key)。

        - 如果有错误，则返回错误信息。

        - 调用 `GetFunctionAndParameters` 函数获取要调用的函数名称和参数名称。

        - 判断要调用的函数名称是否为 `query`，如果条件符合，则调用 `query` 函数。如果条件不符合，说明指定要调用的函数名称不正确，返回错误信息。

        具体实现代码如下：

        ```go
        // 对账本数据进行操作时被自动调用 (query, invoke)
        func (t *HelloChaincode) Invoke(stub shim.ChaincodeStubInterface) peer.Response {
            // 获取调用链码时传递的参数内容(包括要调用的函数名及参数)
            fun, args := stub.GetFunctionAndParameters()
            // 客户意图
            if fun == "query" {
                return query(stub, args)
            }
            return shim.Error("非法操作，指定功能不能实现")
        }
        ```

    - 实现查询函数。函数逻辑步骤说明如下。

        - 获取参数并判断长度是否为 1。

        - 利用第 1 个参数获取对应状态 `GetState(key)`。

        - 判断，如果查询后 `err` 不为空，则返回错误信息。

        - 判断，如果返回的查询结果值为空，则直接返回错误。

        - 如果判断通过且没有问题，则返回成功状态（包含查询结果）。

        函数名称为 `query`，具体实现源码如下：

        ```go
        func query(stub shim.ChaincodeStubInterface, args []string) peer.Response {
            // 检查传递的参数个数是否为 1
            if len(args) != 1 {
                return shim.Error("指定的参数错误，必须且只能指定相应的 Key")
            }
            // 根据指定的 Key 调用 GetState 方法查询数据
            result, err := stub.GetState(args[0])
            if err != nil{
                return shim.Error("根据指定的 " + args[0] + "查询数据时发生错误 ")
            }
            if result == nil {
                return shim.Error("根据指定的 " + args[0] + " 没有查询到相应的数据 ")
            }
            // 返回查询结果
            return shim.Success(result)
        }
        ```

#####链码测试

链码编写完成之后，下一步就是对其进行测试，具体实现步骤如下。

1. 启动网络

    进入 `/opt/go/src/github.com/hyperledger/fabric/scripts/fabric-samples/chaincode-docker-devmode` 目录：

    ```shell
    cd /opt/go/src/github.com/hyperledger/fabric/scripts/fabric-samples/chaincode-docker-devmode
    ```

2. 构建并启动链码

    - 进入链码容器：

        ```shell
        docker exec -it chaincode bash
        ```

    - 编译链码：

        ```shell
        cd hello
        go build
        ```

    - 启动链码：

        ```shell
        CORE_PEER_ADDRESS=peer:7052 CORE_CHAINCODE_ID_NAME=hellocc:0 ./hello
        ```

        命令执行后输出如下信息:

        <br>

        <br>

3. 测试链码

    - 打开一个新的终端窗口，进入 `CLI` 容器：

        ```shell
        docker exec -it cli bash
        ```

    - 安装链码：

        ```shell
        peer chaincode install -p chaincodedev/chaincode/hello -n hellocc -v 0
        ```

    - 实例化链码：

        ```shell
        peer chaincode instantiate -n hellocc -v 0 -c '{"Args":["init","Hello","world"]}' -C myc
        ```

    - 调用链码：根据指定的 `key("Hello")` 查询对应的状态数据。

        ```shell
        peer chaincode query -n hellocc -c '{"Args":["query","Hello"]}'-C myc
        ```

        返回查询结果： World

####编程要求

按要求完成本关卡内容后，点击评测即可。

---
开始你的任务吧，祝你成功！