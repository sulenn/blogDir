# 开发样例-非国密

本开发样例使用标准单群组四节点区块链网络结构，搭建请参考：[安装](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/installation.html)。

在利用SDK进行项目开发时，对智能合约进行操作时需要将Solidity智能合约利用go-sdk的`abigen`工具转换为`Go`文件代码。整体上主要包含了六个流程：

- 准备需要编译的智能合约
- 配置好相应版本的solc编译器
- 构建go-sdk的合约编译工具abigen
- 编译生成go文件
- 准备建立ssl连接需要的证书
- 使用生成的go文件进行合约部署、调用

## HelloWorld样例

1. **准备HelloWorld.sol合约文件**：该合约提供两个接口，分别是get()和set()，用于获取/设置合约变量name。合约内容如下

```solidity
pragma solidity ^0.4.25;

contract HelloWorld {
    string name;

    constructor() public {
        name = "Hello, World!";
    }

    function get() public view returns (string) {
        return name;
    }

    function set(string v) public {
        name = v;
    }
}
```

将该合约文件放置在go-sdk主目录的helloworld文件夹中

2. **安装对应版本的[`solc`编译器](https://github.com/ethereum/solidity/releases/tag/v0.4.25)**：该编译器用于将 sol 合约文件编译成 abi 和 bin 文件，目前FISCO BCOS默认的`solc`编译器版本为0.4.25

```shell
# 该指令在helloworld文件夹中执行
bash ../tools/download_solc.sh -v 0.4.25
```

3. **构建go-sdk的代码生成工具abigen**：该工具用于将 abi 和 bin 文件转换为 go 文件

```shell
# 该指令在helloworld文件夹中执行，编译生成abigen工具
go build ../cmd/abigen
```

4. **编译生成go文件**：先利用solc编译合约文件HelloWorld.sol，生成abi和bin文件

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

5. **准备建立ssl连接需要的证书**：使用build_chain.sh脚本搭建区块链会在./nodes/127.0.0.1/sdk文件夹中生成sdk证书、私钥以及ca证书，需要将这三个文件拷贝至go-sdk主目录
6. **部署合约**：在helloworld文件夹中创建contract文件夹，在contract文件夹中创建helloworld_main.go文件，调用HelloWorld.go部署智能合约

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

7. **调用合约get接口**：在contract文件夹中创建helloworld_get.go文件，调用合约get接口，获取智能合约中name变量存储的值

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

8. **调用合约set接口**：在contract文件夹中创建helloworld_set.go文件，调用合约set接口，设置智能合约中name变量的值

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

## Store样例

1. **准备Store.sol合约文件**：该合约使用一个mapping记录键值对，类似字典的功能。合约内容如下

```solidity
pragma solidity ^0.4.25;

contract Store {
  event ItemSet(bytes32 key, bytes32 value);

  string public version;
  mapping (bytes32 => bytes32) public items;

  constructor(string _version) public {
    version = _version;
  }

  function setItem(bytes32 key, bytes32 value) external {
    items[key] = value;
    emit ItemSet(key, value);
  }
}
```

将该合约文件放置在go-sdk主目录的store文件夹中

2. **安装对应版本的[`solc`编译器](https://github.com/ethereum/solidity/releases/tag/v0.4.25)**：该编译器用于将 sol 合约文件编译成 abi 和 bin 文件，目前FISCO BCOS默认的`solc`编译器版本为0.4.25

```shell
# 该指令在store文件夹中执行
bash ../tools/download_solc.sh -v 0.4.25
```

3. **构建go-sdk的代码生成工具abigen**：该工具用于将 abi 和 bin 文件转换为 go 文件

```shell
# 该指令在store文件夹中执行，编译生成abigen工具
go build ../cmd/abigen
```

4. **编译生成go文件**：先利用solc编译合约文件Store.sol，生成abi和bin文件

```shell
# 该指令在store文件夹中执行
./solc-0.4.25 --bin --abi -o ./ ./Store.sol
```

store目录下会生成Store.bin和Store.abi。此时利用abigen工具将Store.bin和Store.abi转换成Store.go：

```shell
# 该指令在store文件夹中执行
./abigen --bin ./Store.bin --abi ./Store.abi --pkg store --type Store --out ./Store.go
```

最后store文件夹下面存在以下6个文件：

```shell
Store.abi 、Store.bin、Store.go、Store.sol、solc-0.4.25、abigen
```

5. **准备建立ssl连接需要的证书**：使用build_chain.sh脚本搭建区块链会在./nodes/127.0.0.1/sdk文件夹中生成sdk证书、私钥以及ca证书，需要将这三个文件拷贝至go-sdk主目录
6. **部署合约**：在store文件夹中创建contract文件夹，在contract文件夹中创建store_main.go文件，调用Store.go部署智能合约

```go
package main

import (
    "fmt"
    "log"

    "github.com/FISCO-BCOS/go-sdk/client"
    "github.com/FISCO-BCOS/go-sdk/conf"
    "github.com/FISCO-BCOS/go-sdk/store" // import store
)

func main(){
    config := &conf.ParseConfig("config.toml")[0]

    client, err := client.Dial(config)
    if err != nil {
        log.Fatal(err)
    }
    input := "Store deployment 1.0"
    address, tx, instance, err := store.DeployStore(client.GetTransactOpts(), client, input)
    if err != nil {
        log.Fatal(err)
    }
    fmt.Println("contract address: ", address.Hex())  // the address should be saved
    fmt.Println("transaction hash: ", tx.Hash().Hex())
    _ = instance
}
```

> 注意：合约地址需要手动保存，调用合约接口时使用

7. **调用合约查询接口**：在部署过程中设置的Store.sol合约中有一个名为version的全局变量。 因为它是公开的，这意味着它们将成为我们自动创建的getter函数。 常量和view函数也接受bind.CallOpts作为第一个参数，在store文件夹中新建store_read.go文件以查询合约：

```go
package main

import (
	"fmt"
	"log"

	"github.com/FISCO-BCOS/go-sdk/client"
	"github.com/FISCO-BCOS/go-sdk/conf"
	"github.com/FISCO-BCOS/go-sdk/store"
	"github.com/ethereum/go-ethereum/common"
)

func main() {
	config := &conf.ParseConfig("config.toml")[0]
	client, err := client.Dial(config)
	if err != nil {
		log.Fatal(err)
	}

	// load the contract
	contractAddress := common.HexToAddress("contract addree in hex") // 0x0626918C51A1F36c7ad4354BB1197460A533a2B9
	instance, err := store.NewStore(contractAddress, client)
	if err != nil {
		log.Fatal(err)
	}

	storeSession := &store.StoreSession{Contract: instance, CallOpts: *client.GetCallOpts(), TransactOpts: *client.GetTransactOpts()}

	version, err := storeSession.Version()
	if err != nil {
		log.Fatal(err)
	}

	fmt.Println("version :", version) // "Store deployment 1.0"
}
```

8. **调用合约写接口**：写入智能合约需要我们用私钥来对交易事务进行签名，我们创建的智能合约有一个名为setItem的外部方法，它接受solidity bytes32类型的两个参数（key，value）。 这意味着在Go文件中需要传递一个长度为32个字节的字节数组。在store文件夹中新建store_write.go来测试写入智能合约：

```go
package main

import (
	"fmt"
	"log"

	"github.com/FISCO-BCOS/go-sdk/client"
	"github.com/FISCO-BCOS/go-sdk/conf"
	"github.com/FISCO-BCOS/go-sdk/store"
	"github.com/ethereum/go-ethereum/common"
)

func main() {
	config := &conf.ParseConfig("config.toml")[0]
	client, err := client.Dial(config)
	if err != nil {
		log.Fatal(err)
	}

	// load the contract
	contractAddress := common.HexToAddress("contract addree in hex") // 0x0626918C51A1F36c7ad4354BB1197460A533a2B9
	instance, err := store.NewStore(contractAddress, client)
	if err != nil {
		log.Fatal(err)
	}

	storeSession := &store.StoreSession{Contract: instance, CallOpts: *client.GetCallOpts(), TransactOpts: *client.GetTransactOpts()}

	key := [32]byte{}
	value := [32]byte{}
	copy(key[:], []byte("foo"))
	copy(value[:], []byte("bar"))

	tx, err := storeSession.SetItem(key, value)
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

	// read the result
	result, err := storeSession.Items(key)
	if err != nil {
		log.Fatal(err)
	}

	fmt.Println(string(result[:])) // "bar"
}
```

## TableTest样例