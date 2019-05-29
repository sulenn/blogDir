# RPC 远程过程调用

RPC（Remote Procedure Call），即远程过程调用，是一个分布式系统间通信的必备技术。RPC将过程调用的通用编程抽象扩展到了分布式环境。一个调用过程可以像调用本地结点上的过程那样去调用一个远程结点上的过程。

TCP 协议是 RPC 的 基石，一般来说通信是建立在 TCP 协议之上的，而且 RPC 往往需要可靠的通信，因此不采用 UDP。

**个人理解**：可以将 RPC 理解为一种传输层协议，类似 http 协议

**参考**：

- [RPC详解](https://yq.aliyun.com/articles/611352)

- [远程过程调用(RPC)详解](https://waylau.com/remote-procedure-calls/)

- [《分布式系统》读书笔记 - 第五章 远程调用](https://dinghaoli.github.io/2018/10/Distributed_system_5/)