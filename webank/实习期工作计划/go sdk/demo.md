# 开发样例-非国密

本开发样例使用标准单群组四节点区块链网络结构，搭建请参考：[安装](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/installation.html)

在利用SDK进行项目开发时，对智能合约进行操作时需要将Solidity智能合约利用go-sdk的`abigen`工具转换为`Go`文件代码。整体上主要包含了五个流程：

- 准备需要编译的智能合约
- 配置好相应版本的solc编译器
- 构建go-sdk的合约编译工具abigen
- 编译生成go文件
- 使用生成的go文件进行合约部署、调用

## HelloWorld样例

1. 准备HelloWorld.sol合约文件：该合约提供两个接口，分别是get()和set()，用于获取/设置合约变量name。合约内容如下

```solidity
pragma solidity ^0.4.25;

contract HelloWorld {
    string name;

    function HelloWorld() {
        name = "Hello, World!";
    }

    function get()constant returns(string) {
        return name;
    }

    function set(string n) {
        name = n;
    }
}
```

将该合约文件放置在go-sdk主目录的helloworld文件夹中

2. 安装对应版本的[`solc`编译器](https://github.com/ethereum/solidity/releases/tag/v0.4.25)：该编译器用于将 sol 合约文件编译成 abi 和 bin 文件，目前FISCO BCOS默认的`solc`编译器版本为0.4.25

```shell
# 该指令在helloworld文件夹中执行
bash ../tools/download_solc.sh -v 0.4.25
```

3. 构建go-sdk的代码生成工具abigen：该工具用于将 abi 和 bin 文件转换为 go 文件

```shell
# 该指令在helloworld文件夹中执行，编译生成abigen工具
go build ../cmd/abigen
```

4. 编译生成go文件：先利用solc编译合约文件HelloWorld.sol，生成abi和bin文件

```shell
# 该指令在helloworld文件夹中执行
./solc-0.4.25 --bin --abi -o ./ ./HelloWorld.sol
```

helloworld目录下会生成HelloWorld.bin和HelloWorld.abi。此时利用abigen工具将HelloWorld.bin和HelloWorld.abi转换成HelloWorld.go：

```shell
# 该指令在helloworld文件夹中执行
./abigen --bin ./HelloWorld.bin --abi ./HelloWorld.abi --pkg helloworld --type HelloWorld --out ./HelloWorld.go
```

最后helloworld文件夹下面存在以下6个文件：

```shell
HelloWorld.abi 、HelloWorld.bin、HelloWorld.go、HelloWorld.sol、solc-0.4.25、abigen
```

5. 部署合约：在helloworld文件夹中创建contract文件夹，在contract文件夹中创建helloworld_main.go文件，调用helloworld.go部署智能合约

```go
package main

import (
	"fmt"
	"log"

	"github.com/FISCO-BCOS/go-sdk/client"
	"github.com/FISCO-BCOS/go-sdk/conf"
	"github.com/FISCO-BCOS/go-sdk/helloworld" // import helloworld
)

func main(){
	config := &conf.ParseConfig("config.toml")[0]

	client, err := client.Dial(config)
	if err != nil {
		log.Fatal(err)
	}
	address, tx, instance, err := helloworld.DeployHelloWorld(client.GetTransactOpts(), client) // deploy contract
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println("contract address: ", address.Hex())  // the address should be saved
	fmt.Println("transaction hash: ", tx.Hash().Hex())
	_ = instance
}
```

> 注意：合约地址需要手动保存，调用合约接口时使用

6. 调用合约get接口：在contract文件夹中创建helloworld_get.go文件，调用合约get接口，获取智能合约中name变量存储的值

```go
package main

import (
	"fmt"
	"log"

	"github.com/FISCO-BCOS/go-sdk/client"
	"github.com/FISCO-BCOS/go-sdk/conf"
	"github.com/FISCO-BCOS/go-sdk/helloworld"
	"github.com/ethereum/go-ethereum/common"
)

func main() {
	config := &conf.ParseConfig("config.toml")[0]
	client, err := client.Dial(config)
	if err != nil {
		log.Fatal(err)
	}

	// load the contract
	contractAddress := common.HexToAddress("contract addree in hex") // 0x481D3A1dcD72cD618Ea768b3FbF69D78B46995b0
	instance, err := helloworld.NewHelloWorld(contractAddress, client)
	if err != nil {
		log.Fatal(err)
	}

	helloworldSession := &helloworld.HelloWorldSession{Contract: instance, CallOpts: *client.GetCallOpts(), TransactOpts: *client.GetTransactOpts()}

	value, err := helloworldSession.Get()    // call get API
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println("value :", value)
}
```

7. 调用合约set接口：在contract文件夹中创建helloworld_set.go文件，调用合约set接口，设置智能合约中name变量的值

```go
package main

import (
	"fmt"
	"log"

	"github.com/FISCO-BCOS/go-sdk/client"
	"github.com/FISCO-BCOS/go-sdk/conf"
	"github.com/FISCO-BCOS/go-sdk/helloworld"
	"github.com/ethereum/go-ethereum/common"
)

func main() {
	config := &conf.ParseConfig("config.toml")[0]
	client, err := client.Dial(config)
	if err != nil {
		log.Fatal(err)
	}

	// load the contract
	contractAddress := common.HexToAddress("contract addree in hex") // 0x481D3A1dcD72cD618Ea768b3FbF69D78B46995b0
	instance, err := helloworld.NewHelloWorld(contractAddress, client)
	if err != nil {
		log.Fatal(err)
	}

	helloworldSession := &helloworld.HelloWorldSession{Contract: instance, CallOpts: *client.GetCallOpts(), TransactOpts: *client.GetTransactOpts()}

	var value = "Hello, FISCO BCOS"

	tx, err := helloworldSession.Set(value)  // call set API
	if err != nil {
		log.Fatal(err)
	}

	fmt.Printf("tx sent: %s\n", tx.Hash().Hex())

	// wait for the mining
	receipt, err := client.WaitMined(tx)
	if err != nil {
		log.Fatalf("tx mining error:%v\n", err)
	}
	fmt.Printf("transaction hash of receipt: %s\n", receipt.GetTransactionHash())
}
```





