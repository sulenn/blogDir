### /bitcoin_part3/src/coin/main.go
```go
package main

import (
	"core"
)

func main()  {
	bc := core.NewBlockchain()    //创建区块链条
	defer bc.Db.Close()  //main方法结束之后关闭db数据库

	cli := core.CLI{bc}
	cli.Run()
}
```

### /bitcoin_part3/src/core/block.go
```go
package core

import (
	"bytes"
	"crypto/sha256"
	"encoding/gob"
	"log"
	"strconv"
	"time"
)

//Block keeps block headers
type Block struct {
	Timestamp int64 //区块创建时间戳
	Data []byte   //区块包含的数据
	PrevBlockHash []byte //前一个区块的哈希值
	Hash []byte //区块自身的哈希值，用于校验区块数据有效
	Nonce int
}

//Serialize serializes the block
func (b *Block) Serialize() []byte {   //将创世纪块转换为一个字节数组
	var result bytes.Buffer
	encoder := gob.NewEncoder(&result)

	err := encoder.Encode(b)
	if err != nil {
		log.Panic(err)
	}

	return result.Bytes()
}

//NewBlock creates and return Blocks
func NewBlock(data string, prevBlockHash []byte) *Block {
	block := &Block{time.Now().Unix(), []byte(data), prevBlockHash, []byte{}, int(0)}  //Nonce为0是我自己加的
	pow := NewProofofWork(block)
	nonce, hash := pow.Run()

	block.Hash = hash[:]
	block.Nonce = nonce
	return block
}

//SetHash calculates and sets block hash
func (b *Block) SetHash()  {
	timestamp := []byte(strconv.FormatInt(b.Timestamp, 10))     //时间戳转字节数组
	headers := bytes.Join([][]byte{b.PrevBlockHash, b.Data, timestamp}, []byte{})
	hash := sha256.Sum256(headers)
	b.Hash = hash[:]
}

//NewGenesizeBlock creates and returns genesis Block
func NewGenesisBlock() *Block {
	return NewBlock("Genesis Block", []byte{})
}

//DeserializeBlock deserializes a block
func DeserializeBlock(d []byte) *Block {
	var block Block

	decoder := gob.NewDecoder(bytes.NewReader(d))
	err := decoder.Decode(&block)
	if err != nil{
		log.Panic(err)
	}

	return &block
}
```

### /bitcoin_part3/src/core/blockchain.go
```go
package core

import (
	"fmt"
	"github.com/boltdb/bolt"
	"log"
)

const dbFile = "blockchain.db"    //数据库文件名
const blocksBucket = "blocks"

//Blockchain keeps a sequence of Blocks
type Blockchain struct {
	//Blocks []*Block
	tip []byte   //创世纪块的哈希
	Db *bolt.DB    //替代数组，保存内容到本地
}

//BlockchainIterator is used to iterate over blockchain blocks
type BlockchainIterator struct {
	currentHash []byte
	Db *bolt.DB
}



//AddBlock saves provided data as a block in the blockchain
func (bc *Blockchain) AddBlock(data string)  {
	var lastHash []byte

	err := bc.Db.View(func(tx *bolt.Tx) error {
		b := tx.Bucket([]byte(blocksBucket))
		lastHash = b.Get([]byte("1"))

		return nil
	})

	if err != nil{
		log.Panic(err)
	}

	newBlock := NewBlock(data, lastHash)

	err = bc.Db.Update(func(tx *bolt.Tx) error {
		b := tx.Bucket([]byte(blocksBucket))
		err := b.Put(newBlock.Hash, newBlock.Serialize())
		if err != nil{
			log.Panic(err)
		}

		err = b.Put([]byte("1"), newBlock.Hash)
		if err != nil{
			log.Panic(err)
		}

		bc.tip = newBlock.Hash
		return nil
	})
}

//Iterator...
func (bc *Blockchain) Iterator() *BlockchainIterator {
	bci := &BlockchainIterator{bc.tip, bc.Db}

	return bci
}

func (i *BlockchainIterator) Next() *Block {
	var block *Block
	err := i.Db.View(func(tx *bolt.Tx) error {
		b := tx.Bucket([]byte(blocksBucket))
		if b != nil {
			block = DeserializeBlock(b.Get(i.currentHash))
		}

		return nil
	})

	if err != nil {
		log.Panic(err)
	}

	i.currentHash = block.PrevBlockHash

	return block
}

//NewBlockchain creates a new Blockchain with genesis Block
func NewBlockchain() *Blockchain {
	var tip []byte
	db, err := bolt.Open(dbFile, 0600, nil)  //打开某一个硬盘上的文件
	if err != nil{     //文件打开失败，退出
		log.Panic(err)
	}

	err = db.Update(func(tx *bolt.Tx) error {   //向文件中提交数据
		b := tx.Bucket([]byte(blocksBucket))  //数据库中有很多桶，查询是否存在桶"blocks"

		if b == nil{     //第一次不存在这个桶，没有就创建一个桶
			fmt.Println("No existing blockchain found. Creating a new one...")
			genesis := NewGenesisBlock()      //创世区块

			b, err := tx.CreateBucket([]byte(blocksBucket))   //创建一个桶,替代以前的数组
			if err != nil{    //判断桶是否创建成功
				log.Panic(err)
			}
			//key和value都是字节数组
			err = b.Put(genesis.Hash, genesis.Serialize())    //Key/Value的数组，前者为key(本区快哈希)，后者为Value(区块本身的字节)
			if err != nil{
				log.Panic(err)
			}

			err = b.Put([]byte("1"), genesis.Hash) //创世纪块非常重要，所以单独存储创世纪块的哈希，方便查找
			if err != nil{
				log.Panic(err)
			}
			tip = genesis.Hash
		} else {   //桶已经存在
			tip = b.Get([]byte("1"))
		}

		return nil  //?????为什么会返回nil？？？
	})

	if err != nil {
		log.Panic(err)
	}

	bc := Blockchain{tip,db}

	return &bc

}
```

### /bitcoin_part3/src/core/proofofwork.go
```go
package core

import (
	"bytes"
	"crypto/sha256"
	"fmt"
	"math"
	"math/big"
)

var(
	maxNonce = math.MaxInt64   //整数64位里面最大的数值
)

const targetBits  = 8   //目标位

// proofofwork represents a proof-of-work
type ProofofWork struct {
	block *Block
	target *big.Int  //目标（对block区块进行计算，满足这个设定的目标）
}

// NewProofofwork builds and returns a ProofofWork
func NewProofofWork(b *Block) *ProofofWork {
	target := big.NewInt(1)   //整数1
	target.Lsh(target, uint(256 - targetBits)) //对整数前面的bit进行移位操作，前20位变为0
	pow := &ProofofWork{b,target}
	return pow
}

func (pow *ProofofWork) prepareData(nonce int) []byte {
	data := bytes.Join(
		[][]byte{
			pow.block.PrevBlockHash,
			pow.block.Data,
			IntToHex(pow.block.Timestamp),
			IntToHex(int64(targetBits)),
			IntToHex(int64(nonce)),
		},
		[]byte{},
		)
	return data
}

func (pow *ProofofWork) Run() (int, []byte) {
	var hashInt big.Int
	var hash [32]byte
	nonce := 0

	fmt.Printf("Mining the block containing \"%s\"\n", pow.block.Data)
	for nonce < maxNonce {
		data := pow.prepareData(nonce)

		hash = sha256.Sum256(data)
		fmt.Printf("\r%x", hash)
		hashInt.SetBytes(hash[:])   //把哈希值转换成一个整数

		if hashInt.Cmp(pow.target) == -1{
			break
		} else {
			nonce++
		}
	}
	fmt.Print("\n\n")

	return nonce, hash[:]
}

// Validate validates blocks Pow
func (pow *ProofofWork) Validate() bool {
	var hashInt big.Int

	data := pow.prepareData(pow.block.Nonce)
	hash := sha256.Sum256(data)
	hashInt.SetBytes(hash[:])

	isValid := hashInt.Cmp(pow.target) == -1
	return isValid
}
```

### /bitcoin_part3/src/core/utils.go
```go
package core

import (
	"bytes"
	"crypto/sha256"
	"encoding/binary"
	"log"
)

// IntToHex converts an int64 to a byte array
func IntToHex(num int64) []byte {
	buff := new(bytes.Buffer)
	err := binary.Write(buff, binary.BigEndian, num)
	if err != nil{
		log.Panic(err)
	}

	return buff.Bytes()
}

func DataToHash(data []byte) []byte {
	hash := sha256.Sum256(data)
	return hash[:]
}
```

### /bitcoin_part3/src/core/cli.go
```go
package core

import (
	"flag"
	"fmt"
	"log"
	"os"
	"strconv"
)

// CLI responsible for processing command line arguments
type CLI struct {
	Bc *Blockchain
}

func (cli *CLI) printUsage()  {
	fmt.Println("Usage:")
	fmt.Println("  addblock -data BLOCK_DATA - add a block to the blockchain")
	fmt.Println("  printchain - print all the block of the blockchain")
}

func (cli *CLI) validateArgs()  {   //解析参数
	if len(os.Args) < 2 {
		cli.printUsage()      //如果没有参数就打印对应的用法
		os.Exit(1)
	}
}

func (cli *CLI) addBlock(data string)  {
	cli.Bc.AddBlock(data)
	fmt.Println("Success!")
}

func (cli *CLI) printChain()  {
	bci := cli.Bc.Iterator()

	for {
		block := bci.Next()

		fmt.Printf("Prev. hash: %x\n", block.PrevBlockHash)
		fmt.Printf("Data: %s\n", block.Data)
		fmt.Printf("Hash: %x\n", block.Hash)
		pow := NewProofofWork(block)
		fmt.Printf("Pow: %s\n", strconv.FormatBool(pow.Validate()))
		fmt.Println()

		if len(block.PrevBlockHash) == 0 {
			break
		}
	}
}

//Run parses command line arguments and processes commands
func (cli *CLI) Run()  {
	cli.validateArgs()  //验证是否在命令行中输入了参数

	addBlockCmd := flag.NewFlagSet("addblock", flag.ExitOnError)    //解析参数用
	printChainCmd := flag.NewFlagSet("printchain", flag.ExitOnError)    //解析参数用

	addBlockData := addBlockCmd.String("data", "", "Block data")

	switch os.Args[1] {
	case "addblock":    //存在添加区块的命令
		err := addBlockCmd.Parse(os.Args[2:])
		if err != nil{
			log.Panic(err)
		}
	case "printchain":    //存在打印区块链的命令
		err := printChainCmd.Parse(os.Args[2:])
		if err != nil{
			log.Panic(err)
		}
	default:   //不存在或者格式错误
		cli.printUsage()
		os.Exit(1)   //退出
	}

	if addBlockCmd.Parsed() {   //如果存在，就添加区块
		if *addBlockData == "" {
			addBlockCmd.Usage()
			os.Exit(1)
		}
		cli.addBlock(*addBlockData)
	}

	if printChainCmd.Parsed() {
		cli.printChain()
	}


}
```


/bitcoin_part3/src/github.com(该目录请在github中自行下载，网址：https://github.com/boltdb/bolt)
代码来自：https://www.imooc.com/video/17557