[toc]

# 开发样例-非国密

本开发样例使用标准单群组四节点区块链网络结构，搭建请参考：[安装](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/installation.html)。

在利用SDK进行项目开发时，对智能合约进行操作时需要利用go-sdk的`abigen`工具将Solidity智能合约转换为`Go`文件代码。整体上主要包含了六个流程：

- 准备需要编译的智能合约
- 配置好相应版本的solc编译器
- 构建go-sdk的合约编译工具abigen
- 编译生成go文件
- 准备建立ssl连接需要的证书
- 使用生成的go文件进行合约部署、调用

## HelloWorld样例

1. **准备HelloWorld.sol合约文件**：在 go-sdk 主目录中新建 helloworld 文件夹，在该文件夹中创建 HelloWorld.sol 合约。该合约提供两个接口，分别是get()和set()，用于获取/设置合约变量name。合约内容如下

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

5. **准备建立ssl连接需要的证书**：使用build_chain.sh脚本搭建区块链时会在./nodes/127.0.0.1/sdk文件夹中生成sdk证书、私钥以及ca证书，需要将这三个文件拷贝至go-sdk主目录
6. **部署合约**：在helloworld文件夹中创建contract文件夹，在contract文件夹中创建helloworld_main.go文件，在该文件中调用HelloWorld.go部署智能合约

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

	value, err := helloworldSession.Get()    // call Get API
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

## TableTest样例

1. **准备Table.sol合约文件**：在 go-sdk 主目录中新建 tabletest 文件夹，在该文件夹中创建 Table.sol 合约。该合约是数据库合约，可以创建表，并对表进行增删该查操作。有关该合约的详细内容可参考：[使用Table合约CRUD接口](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/manual/smart_contract.html#tablecrud)

```sol
pragma solidity ^0.4.24;

contract TableFactory {
    function openTable(string) public constant returns (Table);  // 打开表
    function createTable(string,string,string) public returns(int);  // 创建表
}

// 查询条件
contract Condition {
    //等于
    function EQ(string, int) public;
    function EQ(string, string) public;

    //不等于
    function NE(string, int) public;
    function NE(string, string)  public;

    //大于
    function GT(string, int) public;
    //大于或等于
    function GE(string, int) public;

    //小于
    function LT(string, int) public;
    //小于或等于
    function LE(string, int) public;

    //限制返回记录条数
    function limit(int) public;
    function limit(int, int) public;
}

// 单条数据记录
contract Entry {
    function getInt(string) public constant returns(int);
    function getAddress(string) public constant returns(address);
    function getBytes64(string) public constant returns(byte[64]);
    function getBytes32(string) public constant returns(bytes32);
    function getString(string) public constant returns(string);

    function set(string, int) public;
    function set(string, string) public;
    function set(string, address) public;
}

// 数据记录集
contract Entries {
    function get(int) public constant returns(Entry);
    function size() public constant returns(int);
}

// Table主类
contract Table {
    // 查询接口
    function select(string, Condition) public constant returns(Entries);
    // 插入接口
    function insert(string, Entry) public returns(int);
    // 更新接口
    function update(string, Entry, Condition) public returns(int);
    // 删除接口
    function remove(string, Condition) public returns(int);

    function newEntry() public constant returns(Entry);
    function newCondition() public constant returns(Condition);
}
```



2. **准备TableTest.sol合约文件**：该合约调用了 AMDB 专用的智能合约 Table.sol，实现的是创建用户表 t_test，并对t_test 表进行增删改查的功能。

```solidity
pragma solidity ^0.4.25;
pragma experimental ABIEncoderV2;
import "./Table.sol";

contract TableTest {
    event CreateResult(int256 count);
    event InsertResult(int256 count);
    event UpdateResult(int256 count);
    event RemoveResult(int256 count);

    TableFactory tableFactory;
    string constant TABLE_NAME = "t_test";
    constructor() public {
        //The fixed address is 0x1010 for KVTableFactory
        tableFactory = TableFactory(0x1001); //The fixed address is 0x1001 for TableFactory
        tableFactory.createTable(TABLE_NAME, "name", "item_id,item_name");
    }

    //select records
    function select(string name)
        public
        view
        returns (string[], int256[], string[])
    {
        Table table = tableFactory.openTable(TABLE_NAME);

        Condition condition = table.newCondition();

        Entries entries = table.select(name, condition);
        string[] memory user_name_bytes_list = new string[](
            uint256(entries.size())
        );
        int256[] memory item_id_list = new int256[](uint256(entries.size()));
        string[] memory item_name_bytes_list = new string[](
            uint256(entries.size())
        );

        for (int256 i = 0; i < entries.size(); ++i) {
            Entry entry = entries.get(i);

            user_name_bytes_list[uint256(i)] = entry.getString("name");
            item_id_list[uint256(i)] = entry.getInt("item_id");
            item_name_bytes_list[uint256(i)] = entry.getString("item_name");
        }

        return (user_name_bytes_list, item_id_list, item_name_bytes_list);
    }
    //insert records
    function insert(string name, int256 item_id, string item_name)
        public
        returns (int256)
    {
        Table table = tableFactory.openTable(TABLE_NAME);

        Entry entry = table.newEntry();
        entry.set("name", name);
        entry.set("item_id", item_id);
        entry.set("item_name", item_name);

        int256 count = table.insert(name, entry);
        emit InsertResult(count);

        return count;
    }
    //update records
    function update(string name, int256 item_id, string item_name)
        public
        returns (int256)
    {
        Table table = tableFactory.openTable(TABLE_NAME);

        Entry entry = table.newEntry();
        entry.set("item_name", item_name);

        Condition condition = table.newCondition();
        condition.EQ("name", name);
        condition.EQ("item_id", item_id);

        int256 count = table.update(name, entry, condition);
        emit UpdateResult(count);

        return count;
    }
    //remove records
    function remove(string name, int256 item_id) public returns (int256) {
        Table table = tableFactory.openTable(TABLE_NAME);

        Condition condition = table.newCondition();
        condition.EQ("name", name);
        condition.EQ("item_id", item_id);

        int256 count = table.remove(name, condition);
        emit RemoveResult(count);

        return count;
    }
}
```

3. **安装对应版本的[`solc`编译器](https://github.com/ethereum/solidity/releases/tag/v0.4.25)**：该编译器用于将 sol 合约文件编译成 abi 和 bin 文件，目前 FISCO BCOS 默认的`solc`编译器版本为 0.4.25

```shell
# 该指令在 tabletest 文件夹中执行
bash ../tools/download_solc.sh -v 0.4.25
```

4. **构建 go-sdk 的代码生成工具 abigen**：该工具用于将 abi 和 bin 文件转换为 go 文件

```shell
# 该指令在 tabletest 文件夹中执行，编译生成 abigen 工具
go build ../cmd/abigen
```

5. **编译生成 go 文件**：先利用 solc 编译合约文件 TableTest.sol，生成 abi 和 bin 文件

```shell
# 该指令在 tabletest 文件夹中执行
./solc-0.4.25 --bin --abi -o ./ ./TableTest.sol
```

tabletest 目录下会生成 TableTest.bin、TableTest.abi和其它一些文件。此时利用 abigen 工具将 TableTest.bin 和 TableTest.abi 转换成 TableTest.go：

```shell
# 该指令在 tabletest 文件夹中执行
./abigen --bin ./TableTest.bin --abi ./TableTest.abi --pkg tabletest --type TableTest --out ./TableTest.go
```

最后 tabletest 文件夹下面存在以下6个文件和其它若干文件：

```shell
TableTest.abi 、TableTest.bin、TableTest.go、TableTest.sol、solc-0.4.25、abigen
```

6. **准备建立ssl连接需要的证书**：使用build_chain.sh脚本搭建区块链时会在./nodes/127.0.0.1/sdk文件夹中生成sdk证书、私钥以及ca证书，需要将这三个文件拷贝至go-sdk主目录

7. **部署合约**：在 tabletest 文件夹中创建 contract 文件夹，在 contract 文件夹中创建 tabletest_main.go 文件，调用 TableTest.go 部署智能合约。合约将创建用户表 t_test，该表有三个字段 name、item_id 和 item_name，用于记录某公司员工领用的物资和编号信息。

```go
package main

import (
	"fmt"
	"log"

	"github.com/FISCO-BCOS/go-sdk/client"
	"github.com/FISCO-BCOS/go-sdk/conf"
	table "github.com/FISCO-BCOS/go-sdk/tabletest" // import tabletest
)

func main(){
	config := &conf.ParseConfig("config.toml")[0]

	client, err := client.Dial(config)
	if err != nil {
		log.Fatal(err)
	}
	address, tx, instance, err := table.DeployTableTest(client.GetTransactOpts(), client)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println("contract address: ", address.Hex())  // the address should be saved
	fmt.Println("transaction hash: ", tx.Hash().Hex())
	_ = instance
}
```

> 注意：合约地址需要手动保存，调用合约接口时使用

8. **调用合约Insert接口**：在 contract 文件夹中新建 contract_insert.go 文件，该文件调用合约 Insert 接口，向用户表 t_test 插入一条数据：name="Bob"、item_id=100010001001、item_name="Laptop"。

```go
package main

import (
	"fmt"
	"log"
	"math/big"
	"strconv"

	"github.com/FISCO-BCOS/go-sdk/client"
	"github.com/FISCO-BCOS/go-sdk/conf"
	table "github.com/FISCO-BCOS/go-sdk/tabletest"
	"github.com/ethereum/go-ethereum/common"
)

func main() {
	config := &conf.ParseConfig("config.toml")[0]
	client, err := client.Dial(config)
	if err != nil {
		log.Fatal(err)
	}

	// load the contract
	contractAddress := common.HexToAddress("contract addree in hex") // 0x9526BDd51d7F346ec2B48192f25a800825A8dBF3
	instance, err := table.NewTableTest(contractAddress, client)
	if err != nil {
		log.Fatal(err)
	}

	tabletestSession := &table.TableTestSession{Contract: instance, CallOpts: *client.GetCallOpts(), TransactOpts: *client.GetTransactOpts()}

	name := "Bob"
	item_id := big.NewInt(100010001001)
	item_name := "Laptop"
	tx, err := tabletestSession.Insert(name,item_id,item_name)    // call Insert API
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
	insertedLines, err := strconv.Atoi(receipt.Output[2:])
	if err != nil {
		log.Fatalf("error when transfer string to int: %v\n", err)
	}
	fmt.Printf("inserted lines: %v\n", insertedLines)
}
```

9. **调用合约select接口**：在 contract 文件夹中新建 contract_select.go 文件，该文件调用合约 select 接口，查看用户 "Bob" 在用户表 t_test 中的数据。

```go
package main

import (
	"fmt"
	"log"

	"github.com/FISCO-BCOS/go-sdk/client"
	"github.com/FISCO-BCOS/go-sdk/conf"
	table "github.com/FISCO-BCOS/go-sdk/tabletest"
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
	instance, err := table.NewTableTest(contractAddress, client)
	if err != nil {
		log.Fatal(err)
	}

	tabletestSession := &table.TableTestSession{Contract: instance, CallOpts: *client.GetCallOpts(), TransactOpts: *client.GetTransactOpts()}

	name := "Bob"

	names, item_ids, item_names, err := tabletestSession.Select(name)  // call select API
	if err != nil {
		log.Fatal(err)
	}
	for i:=0; i<len(names); i++ {
		fmt.Printf("name: %v, item_id: %v, item_name: %v \n", names[i], item_ids[i], item_names[i])
	}

}
```

10. **调用合约update接口**：在 contract 文件夹中新建 contract_update.go 文件，该文件调用合约 update 接口，更新用户表 t_test 中用户 "Bob" 的数据，将 item_name 从 Laptop 修改为 Macbook Pro。

```go
package main

import (
	"fmt"
	"log"
	"math/big"
	"strconv"

	"github.com/FISCO-BCOS/go-sdk/client"
	"github.com/FISCO-BCOS/go-sdk/conf"
	table "github.com/FISCO-BCOS/go-sdk/tabletest"
	"github.com/ethereum/go-ethereum/common"
)

func main() {
	config := &conf.ParseConfig("config.toml")[0]
	client, err := client.Dial(config)
	if err != nil {
		log.Fatal(err)
	}

	// load the contract
	contractAddress := common.HexToAddress("contract addree in hex") // 0x9526BDd51d7F346ec2B48192f25a800825A8dBF3
	instance, err := table.NewTableTest(contractAddress, client)
	if err != nil {
		log.Fatal(err)
	}

	tabletestSession := &table.TableTestSession{Contract: instance, CallOpts: *client.GetCallOpts(), TransactOpts: *client.GetTransactOpts()}

	name := "Bob"
	item_id := big.NewInt(100010001001)
	item_name := "Macbook Pro"
	tx, err := tabletestSession.Update(name,item_id,item_name)    // call Update API
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
	updatedLines, err := strconv.Atoi(receipt.Output[2:])
	if err != nil {
		log.Fatalf("error when transfer string to int: %v\n", err)
	}
	fmt.Printf("updated lines: %v\n", updatedLines)
}
```

11. **调用合约remove接口**：在 contract 文件夹中新建 contract_remove.go 文件，该文件调用合约 remove 接口，删除用户表 t_test 中用户 "Bob" 的数据。

```go
package main

import (
	"fmt"
	"log"
	"math/big"
	"strconv"

	"github.com/FISCO-BCOS/go-sdk/client"
	"github.com/FISCO-BCOS/go-sdk/conf"
	table "github.com/FISCO-BCOS/go-sdk/tabletest"
	"github.com/ethereum/go-ethereum/common"
)

func main() {
	config := &conf.ParseConfig("config.toml")[0]
	client, err := client.Dial(config)
	if err != nil {
		log.Fatal(err)
	}

	// load the contract
	contractAddress := common.HexToAddress("0xE46EE87f19049d5705989f49305b136d0732a0Cf") // 0x9526BDd51d7F346ec2B48192f25a800825A8dBF3
	instance, err := table.NewTableTest(contractAddress, client)
	if err != nil {
		log.Fatal(err)
	}

	tabletestSession := &table.TableTestSession{Contract: instance, CallOpts: *client.GetCallOpts(), TransactOpts: *client.GetTransactOpts()}

	name := "Bob"
	item_id := big.NewInt(100010001001)
	tx, err := tabletestSession.Remove(name,item_id)    // call Remove API
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
	removedLines, err := strconv.Atoi(receipt.Output[2:])
	if err != nil {
		log.Fatalf("error when transfer string to int: %v\n", err)
	}
	fmt.Printf("removed lines: %v\n", removedLines)
}
```

## KVTableTest样例

1. **准备KVTable.sol合约文件**：在 go-sdk 主目录中新建 kvtabletest 文件夹，在该文件夹中创建 KVTable.sol 合约。有关该合约的详细内容可参考：[使用KVTable合约读写接口](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/manual/smart_contract.html#kvtable)

```sol
pragma solidity ^0.4.24;

// 单条数据记录
contract Entry {
    function getInt(string) public constant returns(int);
    function getAddress(string) public constant returns(address);
    function getBytes64(string) public constant returns(byte[64]);
    function getBytes32(string) public constant returns(bytes32);
    function getString(string) public constant returns(string);

    function set(string, int) public;
    function set(string, string) public;
    function set(string, address) public;
}

contract KVTableFactory {
    function openTable(string) public view returns (KVTable);
    function createTable(string, string, string) public returns (int256);
}

//KVTable per permiary key has only one Entry
contract KVTable {
    function get(string) public view returns (bool, Entry);
    function set(string, Entry) public returns (int256);
    function newEntry() public view returns (Entry);
}
```

2. **准备KVTableTest.sol合约文件**：该合约调用了 KVTable 合约，实现的是创建用户表 t_kvtest，并对 t_kvtest 表进行读写的功能。

```solidity
pragma solidity ^0.4.24;
import "./KVTable.sol";

contract KVTableTest {
    event SetResult(int256 count);

    KVTableFactory tableFactory;
    string constant TABLE_NAME = "t_kvtest";

    constructor() public {
        //The fixed address is 0x1010 for KVTableFactory
        tableFactory = KVTableFactory(0x1010);
        // the parameters of createTable are tableName,keyField,"vlaueFiled1,vlaueFiled2,vlaueFiled3,..."
        tableFactory.createTable(TABLE_NAME, "id", "item_price,item_name");
    }

    //get record
    function get(string id) public view returns (bool, int256, string) {
        KVTable table = tableFactory.openTable(TABLE_NAME);
        bool ok = false;
        Entry entry;
        (ok, entry) = table.get(id);
        int256 item_price;
        string memory item_name;
        if (ok) {
            item_price = entry.getInt("item_price");
            item_name = entry.getString("item_name");
        }
        return (ok, item_price, item_name);
    }

    //set record
    function set(string id, int256 item_price, string item_name)
        public
        returns (int256)
    {
        KVTable table = tableFactory.openTable(TABLE_NAME);
        Entry entry = table.newEntry();
        // the length of entry's field value should < 16MB
        entry.set("id", id);
        entry.set("item_price", item_price);
        entry.set("item_name", item_name);
        // the first parameter length of set should <= 255B
        int256 count = table.set(id, entry);
        emit SetResult(count);
        return count;
    }
}
```

3. **安装对应版本的[`solc`编译器](https://github.com/ethereum/solidity/releases/tag/v0.4.25)**：该编译器用于将 sol 合约文件编译成 abi 和 bin 文件，目前 FISCO BCOS 默认的`solc`编译器版本为 0.4.25

```shell
# 该指令在 kvtabletest 文件夹中执行
bash ../tools/download_solc.sh -v 0.4.25
```

4. **构建 go-sdk 的代码生成工具 abigen**：该工具用于将 abi 和 bin 文件转换为 go 文件

```shell
# 该指令在 kvtabletest 文件夹中执行，编译生成 abigen 工具
go build ../cmd/abigen
```

5. **编译生成 go 文件**：先利用 solc 编译合约文件 KVTableTest.sol，生成 abi 和 bin 文件

```shell
# 该指令在 kvtabletest 文件夹中执行
./solc-0.4.25 --bin --abi -o ./ ./KVTableTest.sol
```

kvtabletest 目录下会生成 KVTableTest.bin、KVTableTest.abi和其它一些文件。此时利用 abigen 工具将 KVTableTest.bin 和 KVTableTest.abi 转换成 KVTableTest.go：

```shell
# 该指令在 kvtabletest 文件夹中执行
./abigen --bin ./KVTableTest.bin --abi ./KVTableTest.abi --pkg kvtabletest --type KVTableTest --out ./KVTableTest.go
```

最后 kvtabletest 文件夹下面存在以下6个文件和其它若干文件：

```shell
KVTableTest.abi 、KVTableTest.bin、KVTableTest.go、KVTableTest.sol、solc-0.4.25、abigen
```

6. **准备建立ssl连接需要的证书**：使用build_chain.sh脚本搭建区块链时会在./nodes/127.0.0.1/sdk文件夹中生成sdk证书、私钥以及ca证书，需要将这三个文件拷贝至go-sdk主目录

7. **部署合约**：在 kvtabletest 文件夹中创建 contract 文件夹，在 contract 文件夹中创建 kvtabletest_main.go 文件，调用 KVTableTest.go 部署智能合约。合约将创建 t_kvtest 表，该表用于记录某公司仓库中物资，以唯一的物资编号作为主key，保存物资的名称和价格。

```go
package main

import (
	"fmt"
	"log"

	"github.com/FISCO-BCOS/go-sdk/client"
	"github.com/FISCO-BCOS/go-sdk/conf"
	kvtable "github.com/FISCO-BCOS/go-sdk/kvtabletest" // import kvtabletest
)

func main(){
	config := &conf.ParseConfig("config.toml")[0]

	client, err := client.Dial(config)
	if err != nil {
		log.Fatal(err)
	}
	address, tx, instance, err := kvtable.DeployKVTableTest(client.GetTransactOpts(), client)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println("contract address: ", address.Hex())  // the address should be saved
	fmt.Println("transaction hash: ", tx.Hash().Hex())
	_ = instance
}
```

> 注意：合约地址需要手动保存，调用合约接口时使用

8. **调用合约set接口**：在 contract 文件夹中新建 contract_set.go 文件，该文件调用合约 set 接口，向 t_kvtest 表中插入一条数据：id="100010001001"、item_name="Laptop"、item_price=6000。

```go
package main

import (
	"fmt"
	"log"
	"math/big"
	"strconv"

	"github.com/FISCO-BCOS/go-sdk/client"
	"github.com/FISCO-BCOS/go-sdk/conf"
	kvtable "github.com/FISCO-BCOS/go-sdk/kvtabletest"
	"github.com/ethereum/go-ethereum/common"
)

func main() {
	config := &conf.ParseConfig("config.toml")[0]
	client, err := client.Dial(config)
	if err != nil {
		log.Fatal(err)
	}

	// load the contract
	contractAddress := common.HexToAddress("contract addree in hex") // 0x9526BDd51d7F346ec2B48192f25a800825A8dBF3
	instance, err := kvtable.NewKVTableTest(contractAddress, client)
	if err != nil {
		log.Fatal(err)
	}

	kvtabletestSession := &kvtable.KVTableTestSession{Contract: instance, CallOpts: *client.GetCallOpts(), TransactOpts: *client.GetTransactOpts()}

	id := "100010001001"
	item_name := "Laptop"
	item_price := big.NewInt(6000)
	tx, err := kvtabletestSession.Set(id,item_price,item_name)    // call set API
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
	setedLines, err := strconv.Atoi(receipt.Output[2:])
	if err != nil {
		log.Fatalf("error when transfer string to int: %v\n", err)
	}
	fmt.Printf("seted lines: %v\n", setedLines)
}
```

9. **调用合约get接口**：在 contract 文件夹中新建 contract_get.go 文件，该文件调用合约 get 接口，查看id="100010001001" 在表 t_kvtest 中的数据。

```go
package main

import (
	"fmt"
	"log"

	"github.com/FISCO-BCOS/go-sdk/client"
	"github.com/FISCO-BCOS/go-sdk/conf"
	kvtable "github.com/FISCO-BCOS/go-sdk/kvtabletest"
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
	instance, err := kvtable.NewKVTableTest(contractAddress, client)
	if err != nil {
		log.Fatal(err)
	}

	kvtabletestSession := &kvtable.KVTableTestSession{Contract: instance, CallOpts: *client.GetCallOpts(), TransactOpts: *client.GetTransactOpts()}

	id := "100010001001"

	bool, item_price, item_name, err := kvtabletestSession.Get(id)  // call get API
	if err != nil {
		log.Fatal(err)
	}
	if !bool {
		log.Fatalf("id：%v is not found \n", id)
	}
	fmt.Printf("id: %v, item_price: %v, item_name: %v \n", id, item_price, item_name)
}
```

# 开发样例-国密

使用国密特性的开发流程和非国密大致相同，不同在于以下几部分：

1. 搭建的 FISCO BCOS 区块链网络需要开启国密特性，可参考：[国密支持](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/manual/guomi_crypto.html)
2. go-sdk 的 config.toml 配置文件中 KeyFile 配置项，需要将非国密私钥替换为国密私钥
3. go-sdk 的 config.toml 配置文件中 SMCrypto 配置项，需要修改为 true
4. 安装 solc 编译器时需要添加 **-g** 选项，替换为国密版本
5. 使用 abigen 工具将 bin 和 abi 转换为 go 文件时，需要添加参数 **--smcrypto=true**

## HelloWorld样例

1. **准备HelloWorld.sol合约文件**：在 go-sdk 主目录中新建 helloworld 文件夹，在该文件夹中创建 HelloWorld.sol 合约。该合约提供两个接口，分别是get()和set()，用于获取/设置合约变量name。合约内容如下

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

2. **安装对应版本的国密 [solc编译器](https://github.com/ethereum/solidity/releases/tag/v0.4.25)**：该编译器用于将 sol 合约文件编译成 abi 和 bin 文件

```shell
# 该指令在helloworld文件夹中执行
bash ../tools/download_solc.sh -v 0.4.25 -g
```

3. **构建go-sdk的代码生成工具abigen**：该工具用于将 abi 和 bin 文件转换为 go 文件

```shell
# 该指令在helloworld文件夹中执行，编译生成abigen工具
go build ../cmd/abigen
```

4. **编译生成go文件**：先利用solc编译合约文件HelloWorld.sol，生成abi和bin文件

```shell
# 该指令在helloworld文件夹中执行
./solc-0.4.25-gm --bin --abi -o ./ ./HelloWorld.sol
```

helloworld目录下会生成HelloWorld.bin和HelloWorld.abi。此时利用abigen工具将HelloWorld.bin和HelloWorld.abi转换成HelloWorld.go：

```shell
# 该指令在helloworld文件夹中执行
./abigen --bin ./HelloWorld.bin --abi ./HelloWorld.abi --pkg helloworld --type HelloWorld --out ./HelloWorld.go --smcrypto=true
```

5. 接下来的步骤同非国密，不占用多余篇幅
