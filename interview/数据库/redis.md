[toc]

# redis

## redis 最适合的场景

- 会话缓存（Session Cache）

    最常用的一种使用Redis的情景是会话缓存（session cache）。用Redis缓存会话比其他存储（如Memcached）的优势在于：Redis提供持久化。

- 消息队列

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

## 支持的数据类型/Redis底层如何存储数据

五种数据类型：string（字符串），hash（哈希），list（列表），set（集合）及zset(sorted set：有序集合)。

*请参考链接3，最详细，没有之一*

**参考**：

- [https://www.runoob.com/redis/redis-data-types.html](https://www.runoob.com/redis/redis-data-types.html)

- [Redis（三）--- Redis的五大数据类型的底层实现](https://www.cnblogs.com/MouseDong/p/11134039.html)

- [最详细，没有之一](https://www.w3cschool.cn/hdclil/5ls6tozt.html)

---

## redis底层数据结构有哪些

*请参考链接3，最详细，没有之一*

redis 使用 c 语言编写

1. 简单动态字符串(SDS)

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gdlm4131gfj30ig07tjs4.jpg)

    **定义**：

    1、len 保存了SDS保存字符串的长度

    2、buf[] 数组用来保存字符串的每个元素

    3、free j记录了 buf 数组中未使用的字节数量

    **优点**：

    1. 常数复杂度获取字符串长度;C语言中字符串本身不记录长度

    2. 减少修改字符串的内存重新分配次数；

    3. 杜绝缓冲区溢出：C语言有发生缓冲区溢出的可能性，如“strcat(s1, " Cluster")”将 s1 的内容修改为 "Redis Cluster"时，可能会溢出

        ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gdwzijyrjsj30rs080aas.jpg)

        ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gdwzj5fn4aj30p407s3z8.jpg)

    4. 二进制安全：C语言中字符串除了末尾之外， 字符串里面不能包含空字符，所以C字符串只能保存文本数据，而不能保存像图片、音频、视频、压缩文件这样的二进制数据。但是SDS可以

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

    - ==跳跃表是有序集合的底层实现之一， 除此之外它在 Redis 中没有其他应用。==

    - Redis 的跳跃表实现由 zskiplist 和 zskiplistNode 两个结构组成， 其中 zskiplist 用于保存跳跃表信息（比如表头节点、表尾节点、长度）， 而 zskiplistNode 则用于表示跳跃表节点。

    - 每个跳跃表节点的层高都是 1 至 32 之间的随机数。

    - 在同一个跳跃表中， 多个节点可以包含相同的分值， 但每个节点的成员对象必须是唯一的。

    - 跳跃表中的节点按照分值大小进行排序， 当分值相同时， 节点按照成员对象的大小进行排序。

5. 整数集合

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gdln9gyog8j31970g7dih.jpg)

    ![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gdln9qbet5j314s0agjtp.jpg)

6. 压缩列表

    压缩列表的原理：压缩列表并不是对数据利用某种算法进行压缩，而是将数据按照一定规则编码在一块连续的内存区域，目的是节省内存。

    参考链接3，很详细

**参考**：

- [Redis详解（四）------ redis的底层数据结构](https://www.cnblogs.com/ysocean/p/9080942.html)

- [Redis（三）--- Redis的五大数据类型的底层实现](https://www.cnblogs.com/MouseDong/p/11134039.html)

- [最详细，没有之一](https://www.w3cschool.cn/hdclil/5ls6tozt.html)

- [图解Redis之数据结构篇——压缩列表](https://www.cnblogs.com/hunternet/p/11306690.html)

---

## Redis 有序集合的原理

**参考**：[Redis:有序集合类型zset实现原理](https://www.jianshu.com/p/35bce2ea5743)

---

## Redis 实现分布式锁

**参考**：

1. [分布式锁的实现之 redis 篇](https://xiaomi-info.github.io/2019/12/17/redis-distributed-lock/)
2. [基于Redis的分布式锁实现](https://juejin.im/post/6844903830442737671)

---

## redis 缓存雪崩和穿透

- **雪崩**：Redis挂掉了，请求全部走数据库。*造成缓存雪崩的可能原因*：对缓存数据设置相同的过期时间，导致某段时间内缓存失效，请求全部走数据库。

  **解决方法**：

  ​	1. 在缓存的时候给 *过期时间加上一个随机值*，这样就会大幅度的减少缓存在同一时间过期。

  2. 对于“Redis挂掉了，请求全部走数据库”这种情况，我们可以有以下的思路：

     *事发前*：实现Redis的高可用(**主从架构+Sentinel（哨兵） 或者Redis Cluster（集群）)，尽量避免Redis挂掉这种情况发生**。
     *事发中*：万一Redis真的挂了，我们可以设置**本地缓存**(ehcache)+**限流**(hystrix)，尽量避免我们的数据库被干掉(起码能保证我们的服务还是能正常工作的)
     *事发后*：**redis持久化，重启后自动从磁盘上加载数据，快速恢复缓存数据**。

- **穿透**：请求的数据在缓存大量不命中，导致请求走数据库。

  **解决方法**：

  1. 由于请求的参数是不合法的(每次都请求不存在的参数)，于是我们可以使用**布隆过滤器(BloomFilter)或者压缩filter提前拦截**，不合法就**不让这个请求到数据库层**！
  2. 当我们从数据库找不到的时候，我们也将这个**空对象设置到缓存**里边去。下次再请求的时候，就可以从缓存里边获取了。**这种情况我们一般会将空对象设置一个较短的过期时间**。

**参考**：[Redis缓存雪崩和穿透的解决方法](https://blog.csdn.net/qq_35433716/article/details/86375506)

---

## 数据库和缓存一致性分析（先删缓存再更新数据库？先更新数据库再删缓存？）

写操作针对数据库；读操作针对缓冲，没有命中的话再针对数据库

**不考虑更新缓存**：

1. 如果你是一个写数据库场景比较多，而读数据场景比较少的业务需求，采用这种方案就会导致，数据压根还没读到，缓存就被频繁的更新，浪费性能。

2. 如果你写入数据库的值，并不是直接写入缓存的，而是要经过一系列复杂的计算再写入缓存。那么，每次写入数据库后，都再次计算写入缓存的值，无疑是浪费性能的。显然，删除缓存更为适合。

**优先更新数据库再删除缓存**：

1. 先删除缓存再更新DB

   结论：产生脏数据的概率较大（若出现脏数据，则意味着在不更新的情况下，查询得到的数据均为旧的数据）

   比如：两个并发操作，一个是更新操作，另一个是查询操作，更新操作删除缓存后，查询操作没有命中缓存，先把老数据读出来后放到缓存中，然后更新操作更新了数据库。于是，在缓存中的数据还是老的数据，导致缓存中的数据是脏的，而且还一直这样脏下去了。

2. 先更新DB再删除缓存（使用场景多）

   结论：产生脏数据的概率较小，但是会出现一致性的问题；若更新操作的时候，同时进行查询操作，若hit，则查询得到的数据是旧的数据。但是不会影响后面的查询。（代价较小）

   （ 一个是查询操作，一个是更新操作的并发，首先，没有了删除cache数据的操作了，而是先更新了数据库中的数据，此时，缓存依然有效，所以，并发的查询操作拿的是没有更新的数据，但是，更新操作马上让缓存的失效了，后续的查询操作再把数据从数据库中拉出来。而不会像文章开头的那个逻辑产生的问题，后续的查询操作一直都在取老的数据。）

**并发下解决数据库与缓存不一致的思路：（参考链接3）**

将删除缓存、修改数据库、读取缓存等的操作积压到队列里边，实现串行化。

![image-20200822220430777](https://i.loli.net/2020/08/22/hV5BkTcGFpu2OEi.png)

**参考**：

- [更新数据时，是先删除缓存再更新DB，还是先更新DB再删除缓存？](https://blog.csdn.net/qq_33999844/article/details/81531461)
- [数据库和缓存一致性分析](https://www.cnblogs.com/xmzJava/p/9778109.html)
- [Redis缓存雪崩和穿透的解决方法](https://blog.csdn.net/qq_35433716/article/details/86375506)

---

---