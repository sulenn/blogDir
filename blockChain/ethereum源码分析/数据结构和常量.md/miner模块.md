# miner 模块

位于：/miner/miner.go 43

```go
// Miner creates blocks and searches for proof-of-work values.
type Miner struct {
	mux      *event.TypeMux
	worker   *worker
	coinbase common.Address
	eth      Backend
	engine   consensus.Engine
	exitCh   chan struct{}

	canStart    int32 // can start indicates whether we can start the mining operation
	shouldStart int32 // should start indicates whether we should start after sync
}
```

---

位于：miner/worker.go 39

```go
const (
    // resultQueueSize is the size of channel listening to sealing result.
    //用于监听验证结果的通道（worker.resultCh）的缓存大小。这里的验证结果是已经被签名了的区块
	resultQueueSize = 10

	// txChanSize is the size of channel listening to NewTxsEvent.
    // The number is referenced from the size of tx pool.
    //用于监听事件 core.NewTxsEvent 的通道（worker.txsCh）的缓存大小。这里的缓存大小引用自事务池的大小。其中，事件 core.NewTxsEvent 是事务列表（ []types.Transaction）的封装器
	txChanSize = 4096

    // chainHeadChanSize is the size of channel listening to ChainHeadEvent.
    //用于监听事件 core.ChainHeadEvent 的通道（worker.chainHeadCh）的缓存大小。事件 core.ChainHeadEvent 是区块（types.Block）的封装器
	chainHeadChanSize = 10

    // chainSideChanSize is the size of channel listening to ChainSideEvent.
    //用于监听事件 core.ChainSideEvent 的通道（worker.chainSideCh）的缓存大小。事件 core.ChainSideEvent 是区块（types.Block）的封装器
	chainSideChanSize = 10

    // resubmitAdjustChanSize is the size of resubmitting interval adjustment channel.
    //用于重新提交间隔调整的通道（worker.resubmitAdjustCh）的缓存大小。 缓存的消息结构为 intervalAdjust，用于描述下一次提交间隔的调整因数
	resubmitAdjustChanSize = 10

    // miningLogAtDepth is the number of confirmations before logging successful mining.
    //记录成功挖矿时需要达到的确认数。是 miner.unconfirmedBlocks 的深度 。即本地节点挖出的最新区块如果需要得到整个网络的确认，需要整个网络再挖出 miningLogAtDepth 个区块。举个例子：本地节点挖出了编号为 1 的区块，需要等到整个网络中某个节点（也可以是本地节点）挖出编号为 8 的区块（8 = 1 + miningLogAtDepth, miningLogAtDepth = 7）之后，则编号为 1 的区块就成为了经典链的一部分
	miningLogAtDepth = 7

	// minRecommitInterval is the minimal time interval to recreate the mining block with
    // any newly arrived transactions.
    //使用任何新到达的事务重新创建挖矿区块的最小时间间隔。当用户设定的重新提交间隔太小时进行修正。
	minRecommitInterval = 1 * time.Second

	// maxRecommitInterval is the maximum time interval to recreate the mining block with
    // any newly arrived transactions.
    //使用任何新到达的事务重新创建挖矿区块的最大时间间隔。当用户设定的重新提交间隔太大时进行修正。
	maxRecommitInterval = 15 * time.Second

	// intervalAdjustRatio is the impact a single interval adjustment has on sealing work
    // resubmitting interval.
    //单个间隔调整对验证工作重新提交间隔的影响因子。与参数 intervalAdjustBias 一起决定下一次提交间隔。
	intervalAdjustRatio = 0.1

	// intervalAdjustBias is applied during the new resubmit interval calculation in favor of
    // increasing upper limit or decreasing lower limit so that the limit can be reachable.
    //在新的重新提交间隔计算期间应用intervalAdjustBias，有利于增加上限或减少下限，以便可以访问限制。与参数 intervalAdjustRatio 一起决定下一次提交间隔。
	intervalAdjustBias = 200 * 1000.0 * 1000.0

    // staleThreshold is the maximum depth of the acceptable stale block.
    //可接受的旧区块的最大深度。注意，目前，这个值与 miningLogAtDepth 都是 7，且表达的意思也基本差不多，是不是有一定的内存联系。
	staleThreshold = 7
)
```

位于：miner/worker.go 79

```go
// environment is the worker's current environment and holds all of the current state information.
//描述了 worker 的当前环境，并且包含所有的当前状态信息
type environment struct {
    //签名者，即本地节点的矿工，用于对区块进行签名
	signer types.Signer
    //状态树，用于描述账户相关的状态改变，merkle trie 数据结构。可以在此修改本节节点的状态信息
    state     *state.StateDB // apply state changes here
    //用于检查叔区块的有效性
    ancestors mapset.Set     // ancestor set (used for checking uncle parent validity)
    //用于验证无效叔区块
    family    mapset.Set     // family set (used for checking uncle invalidity)
    //叔区块集合，即当前区块的叔区块集合，或者说当前正在挖的区块的叔区块集合。
    uncles    mapset.Set     // uncle set
    //一个周期里面的事务数量
    tcount    int            // tx count in cycle
    //用于打包事务的可用 gas
	gasPool   *core.GasPool  // available gas used to pack transactions

    header   *types.Header
    //事务（types.Transaction）列表
    txs      []*types.Transaction
    // 收据（types.Receipt）列表。Receipt 表示 Transaction 一一对应的结果。
	receipts []*types.Receipt
}
```

位于：miner/worker.go 95

```go
// task contains all information for consensus engine sealing and result submitting.
// 数据结构 task 包含共识引擎签名和签名之后的结果提交的所有信息。
// 签名即对已经组装好的区块添加最后的签名信息。添加了签名的区块即为最终的结果区块，即签名区块或待确认区块。
// 数据结构 task 和数据结构 environment 的区别：

// 数据结构 environment 用于 worker 的所有操作
// 数据结构 task 仅用于 worker 的签名相关操作

type task struct {
	receipts  []*types.Receipt
    state     *state.StateDB
    //待签名的区块。此时，区块已经全部组装好了，包信了事务列表、叔区块列表。同时，区块头中的字段已经全部组装好了，就差最后的签名。签名后的区块是在此原有区块上新创建的区块，并被发送到结果通道，用于驱动本地节点已经挖出新区块之后的流程。
    block     *types.Block
    //task 的创建时间
	createdAt time.Time
}

// 数据结构 task 也是通道 worker.taskCh 发送或接收的消息
```

位于：miner/worker.go 103

```go
const (
    //无效的中断值
    commitInterruptNone int32 = iota
    // 用于描述新区块头到达的中断值，当 worker 启动或重新启动时也是这个中断值。
    commitInterruptNewHead
    // 用于描述 worker 根据接收到的新事务，中止之前挖矿，并重新开始挖矿。
	commitInterruptResubmit
)
```

位于：miner/worker.go 109

```go
// newWorkReq represents a request for new sealing work submitting with relative interrupt notifier.
// 表示使用相应的中断值通知程序提交新签名工作的请求
// 也是通道 worker.newWorkCh 发送或接收的消息
type newWorkReq struct {
    // 具体的中断值，为 commitInterruptNewHead 或 commitInterruptResubmit 之一
    interrupt *int32
    // 创建的区块是否包含事务
    noempty   bool
    // 区块开始组装的时间
	timestamp int64
}

// intervalAdjust represents a resubmitting interval adjustment.
// 表示重新提交间隔调整
type intervalAdjust struct {
    // 间隔调整的比例
    ratio float64
    // 是上调还是下调
	inc   bool
}
// 在当前区块时计算下一区块的出块大致时间，在基本的时间间隔之上进行一定的微调，微调的参数就是用数据结构 intervalAdjust 描述的，并发送给对应的通道 resubmitAdjustCh。下一个区块在打包时从通道 resubmitAdjustCh 中获取其对应的微调参数 intervalAdjust 实行微调。
```

位于：/miner/worker.go 124

```go
// worker is the main object which takes care of submitting new work to consensus engine
// and gathering the sealing result.
// 负责向共识引擎提交新工作并且收集签名结果的主要对象
type worker struct {
    //区块链的链配置信息，包含链 ID，是 ethash 还是 clique 共识协议等
    config *params.ChainConfig
    // 共识引擎接口
    engine consensus.Engine
    // 后端，包含区块链和事务池，提供挖矿所需的所有方法
    eth    Backend
    // 表示整个区块链
	chain  *core.BlockChain

//  最低 gas
    gasFloor uint64
    // 最高 gas
	gasCeil  uint64

	// Subscriptions
	mux          *event.TypeMux
	txsCh        chan core.NewTxsEvent
	txsSub       event.Subscription
	chainHeadCh  chan core.ChainHeadEvent
	chainHeadSub event.Subscription
	chainSideCh  chan core.ChainSideEvent
	chainSideSub event.Subscription

	// Channels
	newWorkCh          chan *newWorkReq
	taskCh             chan *task
	resultCh           chan *types.Block
	startCh            chan struct{}
	exitCh             chan struct{}
	resubmitIntervalCh chan time.Duration
	resubmitAdjustCh   chan *intervalAdjust

	current      *environment                 // An environment for current running cycle.
	localUncles  map[common.Hash]*types.Block // A set of side blocks generated locally as the possible uncle blocks.
	remoteUncles map[common.Hash]*types.Block // A set of side blocks as the possible uncle blocks.
	unconfirmed  *unconfirmedBlocks           // A set of locally mined blocks pending canonicalness confirmations.

	mu       sync.RWMutex // The lock used to protect the coinbase and extra fields
	coinbase common.Address
	extra    []byte

	pendingMu    sync.RWMutex
	pendingTasks map[common.Hash]*task

	snapshotMu    sync.RWMutex // The lock used to protect the block snapshot and state snapshot
	snapshotBlock *types.Block
	snapshotState *state.StateDB

	// atomic status counters
	running int32 // The indicator whether the consensus engine is running or not.
	newTxs  int32 // New arrival transaction count since last sealing work submitting.

	// External functions
	isLocalBlock func(block *types.Block) bool // Function used to determine whether the specified block is mined by local miner.

	// Test hooks
	newTaskHook  func(*task)                        // Method to call upon receiving a new sealing task.
	skipSealHook func(*task) bool                   // Method to decide whether skipping the sealing.
	fullTaskHook func()                             // Method to call before pushing the full sealing task.
	resubmitHook func(time.Duration, time.Duration) // Method to call upon updating resubmitting interval.
}
```