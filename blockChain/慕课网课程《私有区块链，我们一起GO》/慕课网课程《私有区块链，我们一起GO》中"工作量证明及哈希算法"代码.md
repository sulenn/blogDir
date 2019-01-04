### /bitcoin_part2/src/coin/main.go
```go
package main

import (
	"core"
	"fmt"
	"strconv"
)

func main()  {
	bc := core.NewBlockchain() //初始化区块链，创建第一个区块（创世纪区块）
	bc.AddBlock("Send 1 BTC to Ivan")
	bc.AddBlock("Send 2 more Btc to Ivan")

	for _, block := range bc.Blocks {
		fmt.Printf("Prev. hash: %x\n", block.PrevBlockHash)
		fmt.Printf("Data: %s\n", block.Data)
		fmt.Printf("Hash: %x\n", block.Hash)

		pow := core.NewProofofWork(block)
		fmt.Printf("PoW: %s\n", strconv.FormatBool(pow.Validate()))    //工作量证明
		fmt.Println()
	}
}
```

### /bitcoin_part2/src/core/block.go
```go
package core

import (
	"bytes"
	"crypto/sha256"
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
```

### /bitcoin_part2/src/core/blockchain.go
```go
package core

//Blockchain keeps a sequence of Blocks
type Blockchain struct {
	Blocks []*Block
}

//AddBlock saves provided data as a block in the blockchain
func (bc *Blockchain) AddBlock(data string)  {
	prevBlock := bc.Blocks[len(bc.Blocks)-1] //取出当前区块链中最新的区块
	newBlock := NewBlock(data, prevBlock.Hash)
	bc.Blocks = append(bc.Blocks, newBlock)
}

//NewBlockchain creates a new Blockchain with genesis Block
func NewBlockchain() *Blockchain {
	return &Blockchain{[]*Block{NewGenesisBlock()}}
}
```

### /bitcoin_part2/src/core/proofofwork.go
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

const targetBits  = 20   //目标位

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

### /bitcoin_part2/src/core/utils.go
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

代码来自：https://www.imooc.com/video/17556