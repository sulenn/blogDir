### /bitcoin_part1/src/coin/main.go
```go
package main

import (
	"bitcoin_part1/src/core"
	"fmt"
)

func main()  {
	bc := core.NewBlockchain() //初始化区块链，创建第一个区块（创世纪区块）
	bc.AddBlock("Send 1 BTC to Ivan")
	bc.AddBlock("Send 2 more Btc to Ivan")

	for _, block := range bc.Blocks {
		fmt.Printf("Prev. hash: %x\n", block.PrevBlockHash)
		fmt.Printf("Data: %s\n", block.Data)
		fmt.Printf("Hash: %x\n", block.Hash)
		fmt.Println()
	}
}
```

### /bitcoin_part1/src/core/block.go
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
}

//NewBlock creates and return Blocks
func NewBlock(data string, prevBlockHash []byte) *Block {
	block := &Block{time.Now().Unix(), []byte(data), prevBlockHash, []byte{}}
	block.SetHash()
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

### /bitcoin_part1/src/core/blockchain.go
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

代码来自：https://www.imooc.com/video/17549