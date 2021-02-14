[TOC]

---

####任务描述

本关任务：根据下面的相关知识，完成与 `Hyperledger Fabric` 链码接口以及 `API` 介绍相关的选择题。

####相关知识

开发基于 `Hyperledger Fabric` 的分布式账本应用程序，为了能够调用链码，实现对分布账本数据进行操作，必须使用 `Hyperledger Fabric` 提供的相关 `SDK`。为了方便诸多不同的
应用场景且使用不同语言的开发人员，`Hyperledger Fabric` 提供了许多不同的 `SDK` 来支持各种编程语言。例如：

- `Hyperledger Fabric Node SDK`：[https://github.com/hyperledger/fabric-sdk-node](https://github.com/hyperledger/fabric-sdk-node)。

- `Hyperledger Fabric Java SDK`：[https://github.com/hyperledger/fabric-sdk-java](https://github.com/hyperledger/fabric-sdk-java)。

- `Hyperledger Fabric Python SDK`：[https://github.com/hyperledger/fabric-sdk-py](https://github.com/hyperledger/fabric-sdk-py)。

- `Hyperledger Fabric Go SDK`：[https://github.com/hyperledger/fabric-sdk-go](https://github.com/hyperledger/fabric-sdk-go)。

#####链码接口

启动链码必须通过调用由 `shim` 包中的 `Start` 函数实现，而 `Start` 函数被调用时需要传递一个类型为 `Chaincode` 的参数，这个 `Chaincode` 参数是一个接口类型，该接口中有两个重要的函数：`Init` 与 `Invoke`。

`Chaincode` 接口定义如下：

```go
type Chaincode interface{
    Init(stub ChaincodeStubInterface) peer.Response
    Invoke (stub ChaincodeStubInterface) peer.Response
}
```

编写链码，关键是实现 `Chaincode` 接口中的 `Init` 与 `Invoke` 两个函数。`Hyperledger Fabric` 通过调用 `Invoke` 函数，然后根据不同的请求调用由用户指定的函数来运行事务。

- `Init`：在链码实例化或升级时调用，完成初始化数据的工作。

- `Invoke`：在更新或查询提案事务中的分类账本数据状态时被调用，因此响应调用或查询的业务实现逻辑都需要在此函数中编写实现。

实际开发中，开发人员可以自行定义一个结构体，然后重写 `Chaincode` 接口的两个函数，并将两个函数指定为自定义结构体的成员方法。

#####链码必要结构

`shim` 包为链码提供了用来访问/操作数据状态、事务上下文和调用其他链代码的相关 `API`；`peer` 包提供了链码执行后的响应信息。开发链码需要引入如下依赖包。

1. `github.com/hyperledger/fabric/core/chaincode/shim`。

    - `shim` 包提供了链码与账本交互的中间层。

    - 链码通过 `shim.ChaincodeStub` 提供的相应函数来读取和修改账本的状态。

2. `github.com/hyperledger/fabric/protos/peer`。

    - 链码被调用执行之后通过 `peer` 包中的 `Response` 来封装执行结果的响应信息。

一个开发的链码源文件的必要结构如下：

```go
package main

// 引入必要的包
import {
    "fmt"
    "github.com/hyperledger/fabric/core/chaincode/shim"
    "github.com/hyperledger/fabric/protos/peer"
}
// 声名一个结构体
type SimpleChaincode struct { }

// 为结构体添加 Init 函数
func (t *SimpleChaincode) Init(stub shim.ChaincodeStubInterface) peer.Response {
    // 在该方法中实现链码初始化或升级时的处理逻辑
    // 编写时可灵活使用 stub 中的 API
}

// 为结构体添加 Invoke 函数

func (t *SimpleChaincode) Invoke(stub shim.ChaincodeStubInterface) peer.Response {
    // 在该方法中实现链码运行中被调用或查询时的处理逻辑
    // 编写时可灵活使用 stub 中的 API
}

// 主函数，需要调用 shim.Start() 方法
func main() {
    err := shim.Start(new(SimpleChaincode))
    if err != nil {
        fmt.Printf("Error starting Simple chaincode: %s", err)
    }
}
```

因为链码是一个可独立运行的应用，所以必须声明在一个 `main` 包中，并且提供相应的 `main` 函数作为应用入口。

#####链码 API

我们知道了编写链码的基本接口及所需要的结构，实际中对账本数据进行调用时所需的 `API` 有如下几种类型：

- 参数解析 `API`：调用链码时需要给被调用的目标函数 / 方法传递参数，与参数解析相关的 `API` 提供了获取这些参数（包含被调用的目标函数 / 方法名称）的方法。

- 账本数据状态操作 `API`：该类型的 `API` 提供了对账本数据状态进行操作的方法，包括对状态数据的查询及事务处理等。

- 交易信息获取 `API`：获取提交的交易信息的相关 `API`。

- 事件处理 `API`：与事件处理相关的 `API`。

- 对 `PrivateData` 操作的 `API`：`Hyperledger Fabric` 在 1.2.0 版本中新增的对私有数据操作的相关 `API`。

下面介绍每一种 `API` 的定义及调用时所需参数。

1. 参数解析 `API`

    - `GetArgs() [][]byte`：返回调用链码时在交易提案中指定提供的被调用函数及参数列表。

    - `GetArgsSlice() ([]byte, error)`：返回调用链码时在交易提案中指定提供的参数列表。

    - `GetFunctionAndParameters() (function string, params []string)`：返回调用链码时在交易提案中指定提供的被调用函数名称及其参数列表。

    - `GetStringArgs() []string`：返回调用链码时指定提供的参数列表。

    在实际开发中，常用的获取被调用函数及参数列表的 `API` 一般为 `GetFunctionAndParameters()` 及 `GetStringArgs()` 两个。

2. 账本数据状态操作 `API`

    - `GetState(key string) ([]byte, error)`：根据指定的 `key` 查询相应的数据状态。

    - `PutState(key string, value []byte) eror`：根据指定的`key`，将对应的 `value` 保存在分类账本中。

    - `DelState(key string) error`：根据指定的 `key` 将对应的数据状态删除。

    - `GetStateByRange(startKey, endKey string) (StateQueryIteratorInterface, error)`： 根据指定的开始 `key` 及结束 `key`，查询范围内的所有数据状态。注意，结束 `key`对应的数据状态不包含在返回的结果集中。

    - `GetHistoryForKey(key string) (History QueryIteratorInterface, error)`：根据指定的 `key` 查询所有的历史记录信息。

    - `CreateCompositeKey(objectType string, attributes []string) (string, error)`：创建一个复合键。

    - `SplitCompositeKey(compositekey string) (string, []string, error)`：对指定的复合键进行分割。

    - `GetQueryResult(query string) ( StateQueryIteratorInterface, error)`：对（支持富查询功
    能的）状态数据库进行富查询，目前支持富查询的只有 `CouchDB`。

3. 交易信息获取 `API`

    - `GetTxID() string`：返回交易提案中指定的交易 `ID`。

    - `GetChannelID() string`：返回交易提案中指定的通道 `ID`。

    - `GetTxTimestamp() (*timestamp.Timestamp.error)`：返回交易创建的时间戳，这个时间戳是 `Peer` 接收到交易的具体时间。

    - `GetBinding() ([]byte, error)`：返回交易的绑定信息，如一些临时信息，以避免重复性攻击。

    - `GetSignedProposal() (*pb.SignedProposal, error)`：返回与交易提案相关的签名身份信息。

    - `GetCreator() ([]Byte, error)`：返回该交易提交者的身份信息。

    - `GetTransient() (map[string][]byte, error)`：返回交易中不会被写至账本中的一些临时信息。

4. 事件处理 `API`

    - `SetEvent(name string, payload []byte) eror`：设置事件，包括事件名称及内容。

5. 对 `PrivateData` 操作的 `API`

    - `GetPrivateData(collection, key string) ([]byte,error)`：根据指定的 `key` 从指定的私有数据集中查询对应的私有数据。

    - `PutPrivateData(collection string, key string, value []byte) error`：将指定的 `key` 与 `value` 保存到私有数据集中。

    - `DelPrivateData(collection, key string) eror`：根据指定的 `key` 从私有数据集中删除相应的数据。

    - `GetPrivateDataByRange(collection, startKey, endKey string) (State-QueryIteratorInterface, eror)`：根据指定的开始 `key` 与结束 `key` 查询范围（不包含结束 `key`）内的
    私有数据。

    - `GetPrivateDataByPartialCompositeKey(collection, objectType string, keys []string) (StateQueryIteratorInterface, eror)`：根据给定的部分组合键的集合，查询给定的私有状态。

    - `GetPrivateDataQueryResult(collection, query string) (StateQueryIteratorInterface, error)：根据指定的查询字符串执行富查询（只支持支持富查询的 `CouchDB`）。

####编程要求

根据相关知识，按照要求完成右侧选择题任务，包含单选题和多选题。

---
开始你的任务吧，祝你成功！