# SSR原理和三种代理选择

- 全局和 PAC：全局和 PAC 模式的工作方式是相同的, 它会把你系统的 Internet 代理设置为本地的 SSR node, 所以当你启用全局或者 PAC 模式的时候, 所有遵循 Internet 代理的软件的流量都会被 SSR 所代理. PAC 和 Global 的模式区别是 PAC 有一个列表, 它只会代理列表内的地址或 IP, 列表之外的目的地默认启用直连模式.

  那是否意味着启用 “不代理” 模式的时候, SSR 就不在工作呢? 不是的. SSR client 是始终

- 不代理：那是否意味着启用 “不代理” 模式的时候, SSR 就不在工作呢? 不是的. SSR client 是始终在运行的, 只不过你的 Internet 代理模式没有修改. 有一些软件 (如 SwitchyOmega 浏览器插件) 可以无视 Internet 代理设置而直连代理到本地的 client 节点. 这种情况下, 你选择什么模式并没有任何影响.

**参考**：

- [SSR 原理的简单介绍和代理模式的选择.](https://blog.hiaoxui.com/blog/post/hiaoxui/theory)