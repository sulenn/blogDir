# unconfirmed 详解

如文件名所描述的一样，该文件用于描述存储本地节点挖掘出来的尚未被全网所确认的区块。判断一个区块被全网确认的方法是，最新的区块链中包含该区块，并且该区块后面至少连接了 7 个区块。

所有的 unconfirmed 区块会被放入一个环形链表中，一旦被确认就会从链表中剔除。此外，如果最终发现一个区块既不在区块链中，也不在区块链中区块的叔块中，那么该区块也会从环形链表中剔除。所有的这些关键操作都会以日志的方式记录存证。

![2](http://ww1.sinaimg.cn/large/006alGmrly1g3h0zpn4foj30uz0b8dgf.jpg)

==当前源码版本为：1.9.0-unstable==

```go
// 接口，调用该接口的对象必须要实现该接口中定义的方法。根据区块号获取区块头和区块
type chainRetriever interface {
	// 由区块高度获取区块链中的区块头，用于判断 unconfirmed 区块是否在最新的区块链中充当区块角色
	GetHeaderByNumber(number uint64) *types.Header

	// 由区块高度获取区块链中的区块，用于已经确认unconfirmed 区块不在最新的区块链中充当区块角色之后，判断 unconfirmed 区块是否在最新的区块链充当叔块的角色
	GetBlockByNumber(number uint64) *types.Block
}
```

```go
//本地挖出的 unconfirmed 区块所存储的数据结构
type unconfirmedBlock struct {
	index uint64  //当前区块的高度
	hash  common.Hash   //当前区块 hash，可以参考 worker.go 中 601 行 w.unconfirmed.Insert(block.NumberU64(), block.Hash())
}
```

```go
//最核心的数据结构，可以存储 unconfirmed 区块集合。
type unconfirmedBlocks struct {
	chain  chainRetriever //该属性对应上面的接口定义，用于提供权威的信息证明，判断区块是否被区块链所接纳
	depth  uint           // 区块确认的最小长度，用于判断 unconfirmed 区块同区块链上最新的区块之间的长度关系，以此来决定是否丢弃 unconfirmed 区块
	blocks *ring.Ring     // 存储所有 unconfirmed 区块的环形链表结构
	lock   sync.RWMutex   // 并发访问保护的锁
}
```

```go
//构造函数，初始化 unconfirmedBlocks 数据结构，返回一个指针对象
func newUnconfirmedBlocks(chain chainRetriever, depth uint) *unconfirmedBlocks {
	return &unconfirmedBlocks{
		chain: chain,
		depth: depth,
	}
}
```

```go
//该方法用于往 unconfirmedBlocks 中添加新的 unconfiemed 区块
func (set *unconfirmedBlocks) Insert(index uint64, hash common.Hash) {
	// 移除环形链表中满足条件的 unconfirmed 区块
	set.Shift(index)

	// 根据传入的 index 和 hash 创建新的 unconfirmed 区块，并将它放入 ring 中
	item := ring.New(1)
	item.Value = &unconfirmedBlock{
		index: index,
		hash:  hash,
	}
	// 将新创建的 ring 添加至已有的 blocks 链尾
	set.lock.Lock()
	defer set.lock.Unlock()

	if set.blocks == nil {
		set.blocks = item
	} else {
		set.blocks.Move(-1).Link(item)
	}
	// 用日志的形式通知用户，挖到了一个新的区块
	log.Info("🔨 mined potential block", "number", index, "hash", hash)
}
```

```go
//移除所有超过 unconfirmed 集合长度限制的 unconfirmed 区块。
func (set *unconfirmedBlocks) Shift(height uint64) {
	set.lock.Lock()
	defer set.lock.Unlock()

	for set.blocks != nil {  //for 循环删除
        next := set.blocks.Value.(*unconfirmedBlock)
        //如果当前 unconfirmed 区块的高度 + 确认所需的区块数量长度 > 区块链中最新的区块高度，则 break 跳出当前 for 循环。否则，就判断当前 unconfirmed 区块是否在区块链中，或者是否在区块链区块的叔块中
		if next.index+uint64(set.depth) > height {
			break
		}
		// 根据当前 unconfirmed 区块号获取区块头
        header := set.chain.GetHeaderByNumber(next.index)
        //根据获取的区块头进行对应的处理
		switch {
		case header == nil: //未能获取区块头，日志记录警告
			log.Warn("Failed to retrieve header of mined block", "number", next.index, "hash", next.hash)
		case header.Hash() == next.hash: //区块头对应生成的 hash == 当前 confirmed hash，说明当前 confirmed 区块被全网成功确认，日志记录，恭喜一波
			log.Info("🔗 block reached canonical chain", "number", next.index, "hash", next.hash)
		default:  //当前 confirmed hash 和区块头对应的 hash 值不一致
			// Block is not canonical, check whether we have an uncle or a lost block
			included := false
			for number := next.index; !included && number < next.index+uint64(set.depth) && number <= height; number++ {  //判断当前 unconfirmed 区块是否在区块链剩余区块的叔块中
				if block := set.chain.GetBlockByNumber(number); block != nil {
					for _, uncle := range block.Uncles() {
						if uncle.Hash() == next.hash {  //如果在区块的叔块结构中，就 break 跳出 for 循环
							included = true
							break
						}
					}
				}
			}
			if included {  // 当前 unconfirmed 区块作为叔块存在区块链中，记录一波
				log.Info("⑂ block became an uncle", "number", next.index, "hash", next.hash)
			} else {    //否则，完蛋了，区块丢失，在区块链上没有一丢丢记录，矿工没有拿到一分钱
				log.Info("😱 block lost", "number", next.index, "hash", next.hash)
			}
		}
		// 从环形链表结构中删除当前 unconfirmed 区块
		if set.blocks.Value == set.blocks.Next().Value {
			set.blocks = nil
		} else {
			set.blocks = set.blocks.Move(-1)
			set.blocks.Unlink(1)
			set.blocks = set.blocks.Move(1)
		}
	}
}
```