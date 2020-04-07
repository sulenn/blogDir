[toc]

# redis

## redis 最适合的场景

- 会话缓存（Session Cache）

    最常用的一种使用Redis的情景是会话缓存（session cache）。用Redis缓存会话比其他存储（如Memcached）的优势在于：Redis提供持久化。当维护一个不是严格要求一致性的缓存时，如果用户的购物车信息全部丢失，大部分人都会不高兴的，现在，他们还会这样吗？

- 队列

    Reids在内存存储引擎领域的一大优点是提供 list 和 set 操作，这使得Redis能作为一个很好的消息队列平台来使用。Redis作为队列使用的操作，就类似于本地程序语言（如Python）对 list 的 push/pop 操作。

- 排行榜/计数器

    Redis在内存中对数字进行递增或递减的操作实现的非常好。集合（Set）和有序集合（Sorted Set）也使得我们在执行这些操作的时候变的非常简单，Redis只是正好提供了这两种数据结构。

- 发布/订阅

    最后（但肯定不是最不重要的）是Redis的发布/订阅功能。发布/订阅的使用场景确实非常多。我已看见人们在社交网络连接中使用，还可作为基于发布/订阅的脚本触发器，甚至用Redis的发布/订阅功能来建立聊天系统！

**参考**：[Redis的面试问题总结，来学习下吧](https://m.php.cn/article/415480.html)

---

## 数据持久化

Redis 是内存型数据库，为了保证数据在断电后不会丢失，需要将内存中的数据持久化到硬盘上。

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gclftt65a6j311p0trjwy.jpg)

**参考**:[https://github.com/CyC2018/CS-Notes/blob/master/notes/Redis.md](https://github.com/CyC2018/CS-Notes/blob/master/notes/Redis.md)

---

## redis持久化优化

1. cpu：

    - 不要和cpu密集型服务部署在一起。

2. 内存：

    - 避免大量写入时执行重写操作

    - 单机多实例部署时，尽量同一时刻只有一个子线程在工作(生成RBD或者AOF重写)。

3. 硬盘：

    - 不要和高硬盘负载的应用部署在一起，比如消息队列等。

**参考**：[redis持久化优化](https://blog.csdn.net/qq_27388039/article/details/79943980)

---

## Redis 优势

- 性能极高 – Redis能读的速度是110000次/s,写的速度是81000次/s 。

- 丰富的数据类型 – Redis支持二进制案例的 Strings, Lists, Hashes, Sets 及 Ordered Sets 数据类型操作。

- 原子 – Redis的所有操作都是原子性的，意思就是要么成功执行要么失败完全不执行。单个操作是原子性的。多个操作也支持事务，即原子性，通过MULTI和EXEC指令包起来。

- 丰富的特性 – Redis还支持 publish/subscribe, 通知, key 过期等等特性。

**参考**：[https://www.runoob.com/redis/redis-intro.html](https://www.runoob.com/redis/redis-intro.html)

---

## 支持的数据类型

五种数据类型：string（字符串），hash（哈希），list（列表），set（集合）及zset(sorted set：有序集合)。

**参考**：[https://www.runoob.com/redis/redis-data-types.html](https://www.runoob.com/redis/redis-data-types.html)

---

## Redis底层如何存储数据

**参考**：[Redis（三）--- Redis的五大数据类型的底层实现](https://www.cnblogs.com/MouseDong/p/11134039.html)

---

## redis底层数据结构有哪些

redis 使用 c 语言编写

1. 简单动态字符串(SDS)

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gdlm4131gfj30ig07tjs4.jpg)

    **定义**：

    1、len 保存了SDS保存字符串的长度

    2、buf[] 数组用来保存字符串的每个元素

    3、free j记录了 buf 数组中未使用的字节数量

    **优点**：

    1. 常数复杂度获取字符串长度

    2. 减少修改字符串的内存重新分配次数

2. 链表

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gdlmbwxvb5j30af08x74g.jpg)

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gdlmc61xftj30rv0e3abd.jpg)

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gdlmclcxksj30kz06pmy3.jpg)

    **Redis链表特性**：

    (1)、双端：链表具有前置节点和后置节点的引用，获取这两个节点时间复杂度都为O(1)。

    (2)、无环：表头节点的 prev 指针和表尾节点的 next 指针都指向 NULL,对链表的访问都是以 NULL 结束。　　

    (3)、带链表长度计数器：通过 len 属性获取链表长度的时间复杂度为 O(1)。

3. 哈希表

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gdlmoofzuxj30cp0cjjrz.jpg)

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gdlmp18oc1j30ue0evta3.jpg)

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gdlmpuftyaj319g0d6n0t.jpg)

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gdlmr2yeckj319k0il43n.jpg)

4. 跳跃表

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gdln5pmudqj313s0hktcb.jpg)

5. 整数集合

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gdln9gyog8j31970g7dih.jpg)

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gdln9qbet5j314s0agjtp.jpg)

6. 压缩列表

    压缩列表的原理：压缩列表并不是对数据利用某种算法进行压缩，而是将数据按照一定规则编码在一块连续的内存区域，目的是节省内存。

    参考链接3，很详细

**参考**：

- [Redis详解（四）------ redis的底层数据结构](https://www.cnblogs.com/ysocean/p/9080942.html)

- [Redis（三）--- Redis的五大数据类型的底层实现](https://www.cnblogs.com/MouseDong/p/11134039.html)

- [图解Redis之数据结构篇——压缩列表](https://www.cnblogs.com/hunternet/p/11306690.html)

---

## Redis 有序集合的原理

**参考**：[Redis:有序集合类型zset实现原理](https://www.jianshu.com/p/35bce2ea5743)
