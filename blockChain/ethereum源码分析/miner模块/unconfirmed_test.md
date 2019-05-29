# unconfirmed_test 详解

这是一个测试文件，主要用来测试 unconfirmed.go 中的相关功能。本篇需要先对 unconfirmed.go 文件中的源码内容有一定的了解

还是按照老套路来解析（源码从上到下）

==当前源码版本为：1.9.0-unstable==

```go
//首先是 noopChainRetriever 空结构体的类型定义，在不增加多余内存的目的下实现 unconfirmed.go 文件中 chainRetriever 接口。该接口中有两个方法，如下。这两个方法理论上应该是实现通过区块高度返回对应的区块头和区块功能。但是这里为了测试方便，就全返回了 nil 空值
type noopChainRetriever struct{}

func (r *noopChainRetriever) GetHeaderByNumber(number uint64) *types.Header {
	return nil
}
func (r *noopChainRetriever) GetBlockByNumber(number uint64) *types.Block {
	return nil
}
```

![2](http://ww1.sinaimg.cn/large/006alGmrly1g3h4s8z54rj30x70ivq61.jpg)

上图对应下面 TestUnconfirmedInsertBounds 函数的处理流程

```go
//手动插入若干 unconfirmed 区块至 ring 环形链表中，直到环形链表中最老的 unconfirmed 区块和待插入区块之间的长度大于指定的 depth 长度。然后就将满足条件的 unconfirmed 区块从环形链表中删掉
func TestUnconfirmedInsertBounds(t *testing.T) {
	limit := uint(10) // depth 设置为10，也就是说 unconfirmed 需要经过 10 个区块的验证才可以进行删除操作

    pool := newUnconfirmedBlocks(new(noopChainRetriever), limit) //初始化 unconfirmedBlocks 数据结构
    // 循环便利，从 1 - 10，都是往环形链表中插入 unconfirmed 区块。从 11 开始，边插入新的 unconfirmed 区块边删除旧的 unconfirmed 区块。
	for depth := uint64(0); depth < 2*uint64(limit); depth++ {
		// 每一个层级插入多个 unconfirmed 区块。例如：i=1 时，插入一个 unconfirmed 区块；i=2时，插入两个 unconfirmed 区块。以此累加。
		for i := 0; i < int(depth); i++ {
			pool.Insert(depth, common.Hash([32]byte{byte(depth), byte(i)}))  //往环形链表中插入 unconfirmed 区块。当然，插入的同时也会对满足条件的旧的 unconfirmed 区块进行删除
		}
		// 验证满足 depth 长度要求的 unconfirmed 区块是否已经被删除。注意这里传入的是一个函数，结构有点特殊
		pool.blocks.Do(func(block interface{}) {
			if block := block.(*unconfirmedBlock); block.index+uint64(limit) <= depth {
				t.Errorf("depth %d: block %x not dropped", depth, block.hash)
			}
		})
	}
}
```

```go
//测试 shift 函数处理 unconfirmed 区块的能力。含边界测试
func TestUnconfirmedShifts(t *testing.T) {
	// 设置 depth 长度为 10.区块号 从 25 开始
	limit, start := uint(10), uint64(25)

    //区块号从 25 开始，往 pool 即环形链表中压入 10 个 unconfirmed 区块
	pool := newUnconfirmedBlocks(new(noopChainRetriever), limit)
	for depth := start; depth < start+uint64(limit); depth++ {
		pool.Insert(depth, common.Hash([32]byte{byte(depth)}))
	}
	// 用 34 来测试 shift 方法对边界值的处理
	pool.Shift(start + uint64(limit) - 1)
	if n := pool.blocks.Len(); n != int(limit) {
		t.Errorf("unconfirmed count mismatch: have %d, want %d", n, limit)
	}
	// 测试移除掉一半 unconfirmed 区块
	pool.Shift(start + uint64(limit) - 1 + uint64(limit/2))
	if n := pool.blocks.Len(); n != int(limit)/2 {
		t.Errorf("unconfirmed count mismatch: have %d, want %d", n, limit/2)
	}
	// 测试移除掉所有剩下的 unconfirmed 区块
	pool.Shift(start + 2*uint64(limit))
	if n := pool.blocks.Len(); n != 0 {
		t.Errorf("unconfirmed count mismatch: have %d, want %d", n, 0)
	}
	// 测试 ring 环形链表为空的情况下 Shift 方法对应 的处理能力
	pool.Shift(start + 3*uint64(limit))
	if n := pool.blocks.Len(); n != 0 {
		t.Errorf("unconfirmed count mismatch: have %d, want %d", n, 0)
	}
}
```