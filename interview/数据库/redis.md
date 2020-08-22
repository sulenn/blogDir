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
  2. 当我们从数据库找不到的时候，我们也将这个**空对象设置到缓存**里边去。下次再请求的时候，就可以从缓存里边获取了。
     **这种情况我们一般会将空对象设置一个较短的过期时间**。

**参考**：[Redis缓存雪崩和穿透的解决方法](https://blog.csdn.net/qq_35433716/article/details/86375506)

---

[toc]

# 知识点

## 并发事务可能带来的问题

- 脏读（Dirty read）:

- 丢失修改（Lost to modify）:

- 不可重复读（Unrepeatableread）:

- 幻读（Phantom read）:

**参考**：[https://snailclimb.gitee.io/javaguide/#/docs/database/MySQL](https://snailclimb.gitee.io/javaguide/#/docs/database/MySQL)

---

## 事务隔离级别有哪些

**SQL 标准定义了四个隔离级别**：

- READ-UNCOMMITTED(读取未提交)： 最低的隔离级别，允许读取尚未提交的数据变更，可能会导致脏读、幻读或不可重复读。

- READ-COMMITTED(读取已提交)： 允许读取并发事务已经提交的数据，可以阻止脏读，但是幻读或不可重复读仍有可能发生。

- REPEATABLE-READ(可重复读)： 对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，可以阻止脏读和不可重复读，但幻读仍有可能发生。

  事务 A 多次读取同一数据，事务 B 在事务A多次读取的过程中，对数据作了更新并提交，导致事务A多次读取同一数据时，结果不一致。

- SERIALIZABLE(可串行化)： 最高的隔离级别，完全服从ACID的隔离级别。所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，该级别可以防止脏读、不可重复读以及幻读。

  一个表有10行数据，一个事物 A 两次读取 select * from tale。第一次读取时锁住 10 行数据，读取结果为10。第二次读取之前事务 B 插入了两行，此时表中有 12 行，固两次读取的结果不一致。产生幻读

  *不可重复读的和幻读很容易混淆，不可重复读侧重于**修改**，幻读侧重于**新增**。解决不可重复读的问题只**需锁住满足条件的行**，**解决幻读需要锁表***

**参考**：

- [https://snailclimb.gitee.io/javaguide/#/docs/database/MySQL](https://snailclimb.gitee.io/javaguide/#/docs/database/MySQL)
- [如何理解SQL的可重复读和幻读之间的区别？](https://www.zhihu.com/question/38507762?from=profile_question_card)

---

## 事物的四大特性(ACID)

1. **原子性（Atomicity）：** 事务是最小的执行单位，不允许分割。事务的原子性确保动作要么全部完成，要么完全不起作用；
2. **一致性（Consistency）：** 执行事务前后，数据保持一致，多个事务对同一个数据读取的结果是相同的；
3. **隔离性（Isolation）：** 并发访问数据库时，一个用户的事务不被其他事务所干扰，各并发事务之间数据库是独立的；
4. **持久性（Durability）：** 一个事务被提交之后。它对数据库中数据的改变是持久的，即使数据库发生故障也不应该对其有任何影响。

**参考**：[https://snailclimb.gitee.io/javaguide/#/docs/database/MySQL](https://snailclimb.gitee.io/javaguide/#/docs/database/MySQL)

---

## 大表优化

1. 限定数据的范围

2. 读/写分离

3. 垂直分区

4. 水平分区

**参考**：[https://snailclimb.gitee.io/javaguide/#/docs/database/MySQL](https://snailclimb.gitee.io/javaguide/#/docs/database/MySQL)

---

## 乐观锁和悲观锁

**悲观锁**：

总是假设最坏的情况，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会阻塞直到它拿到锁（**共享资源每次只给一个线程使用，其它线程阻塞，用完后再把资源转让给其它线程**）。传统的关系型数据库里边就用到了很多这种锁机制，比如行锁，表锁等，读锁，写锁等，都是在做操作之前先上锁。Java中synchronized和ReentrantLock等独占锁就是悲观锁思想的实现。

**乐观锁**：

总是假设最好的情况，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，`可以使用版本号机制和CAS算法实现`。**乐观锁适用于多读的应用类型，这样可以提高吞吐量**，像数据库提供的类似于write_condition机制，其实都是提供的乐观锁。在Java中java.util.concurrent.atomic包下面的原子变量类就是使用了乐观锁的一种实现方式CAS实现的。

**两种锁的使用场景**：

从上面对两种锁的介绍，我们知道两种锁各有优缺点，不可认为一种好于另一种，像**乐观锁适用于写比较少的情况下（多读场景）**，即冲突真的很少发生的时候，这样可以省去了锁的开销，加大了系统的整个吞吐量。但如果是多写的情况，一般会经常产生冲突，这就会导致上层应用会不断的进行retry，这样反倒是降低了性能，所以**一般多写的场景下用悲观锁就比较合适**。

**乐观锁常见的两种实现方式**：

1. 版本号机制

2. CAS算法

**参考**：[https://snailclimb.gitee.io/javaguide/#/docs/essential-content-for-interview/%E9%9D%A2%E8%AF%95%E5%BF%85%E5%A4%87%E4%B9%8B%E4%B9%90%E8%A7%82%E9%94%81%E4%B8%8E%E6%82%B2%E8%A7%82%E9%94%81](https://snailclimb.gitee.io/javaguide/#/docs/essential-content-for-interview/%E9%9D%A2%E8%AF%95%E5%BF%85%E5%A4%87%E4%B9%8B%E4%B9%90%E8%A7%82%E9%94%81%E4%B8%8E%E6%82%B2%E8%A7%82%E9%94%81)

---

## mysql 中有哪些锁

- 从类型上来看，可以分为共享锁、排它锁
- 从范围来看，可以分为表锁、行锁，间隙锁、页锁等。
- 其中表锁中又有意向锁。
- 以上锁根据存储引擎不同，生效的锁也不同。

**参考**：[MySQL-浅析间隙锁](https://www.jianshu.com/p/0f3d45c645ac)

---

## mysql 间隙锁

间隙锁（Gap Lock）是Innodb在 *可重复读* 提交下为了解决幻读问题时引入的锁机制，幻读的问题存在是因为新增操作，这时如果进行范围查询的时候（加锁查询），会出现不一致的问题，这时使用不同的行锁已经没有办法满足要求，需要对一定范围内的数据进行加锁，间隙锁就是解决这类问题的。在可重复读隔离级别下，数据库通过行锁和间隙锁共同组成的（next-key lock）来处理幻读问题

**参考**：[MYSQL（04）-间隙锁详解](https://www.jianshu.com/p/32904ee07e56)

---

## MVCC

innodb MVCC主要是为 **Repeatable-Read事务隔离** 级别做的

**参考**：

1. [【mysql】关于innodb中MVCC的一些理解](https://www.cnblogs.com/williamjie/p/9492810.html)

---

## 四种事务隔离级别对应的加锁方案

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gcd4bbxsksj30sq08saac.jpg)

1. 一级加锁协议

   事务在修改数据前必须加X锁，直到事务结束（事务结束包括正常结束(COMMIT)和非正常结束(ROLLBACK)）才可释放；如果仅仅是读数据，不需要加锁。
   一级封锁协议可以防止丢失修改，并保证事务T是可恢复的。使用一级封锁协议可以解决丢失修改问题。
   在一级封锁协议中，如果仅仅是读数据不对其进行修改，是不需要加锁的，它不能保证可重复读和不读“脏”数据。

2. 二级加锁协议

   满足一级加锁协议，且事务在读取数据之前必须先加S锁，读完后即可释放S锁。
   二级封锁协议除防止了丢失修改，还可以进一步防止读“脏”数据。但在二级封锁协议中，由于读完数据后即可释放S锁，所以它不能保证可重复读。

3. 三级加锁协议

   满足一级加锁协议，且事务在读取数据之前必须先加S锁，直到事务结束才释放。
   三级封锁协议除防止了丢失修改和不读“脏”数据外，还进一步防止了不可重复读。

   上述三级协议的主要区别在于什么操作需要申请封锁，以及何时释放。

4. 两段锁协议（2-phase locking）

   两段锁协议是指每个事务的执行可以分为两个阶段：生长阶段（加锁阶段）和衰退阶段（解锁阶段）。
   加锁阶段：在该阶段可以进行加锁操作。在对任何数据进行读操作之前要申请并获得S锁，在进行写操作之前要申请并获得X锁。加锁不成功，则事务进入等待状态，直到加锁成功才继续执行。
   解锁阶段：当事务释放了一个封锁以后，事务进入解锁阶段，在该阶段只能进行解锁操作不能再进行加锁操作。

   若并发执行的所有事务均遵守两段锁协议，则对这些事务的任何并发调度策略都是可串行化的。
   遵循两段锁协议的事务调度处理的结果是可串行化的充分条件，但是可串行化并不一定遵循两段锁协议。

   两段锁协议和防止死锁的一次封锁法的异同之处，一次封锁法要求每个事务必须一次将所有要使用的数据全部加锁，否则就不能继续执行，因此一次封锁法遵守两段锁协议；但是两段锁协议并不要求事务必须一次将所有要使用的数据全部加锁，因此遵守两段锁协议的事务可能发生死锁。

**参考**：[数据库并发事务中的问题与解决方案](https://blog.csdn.net/u012739535/article/details/76855728)

---

## MySQL 索引底层实现类型

- B+Tree 索引

- 哈希索引

- 全文索引

- 空间数据索引

**参考**：[https://github.com/CyC2018/CS-Notes/blob/master/notes/MySQL.md#sharding-%E7%AD%96%E7%95%A5](https://github.com/CyC2018/CS-Notes/blob/master/notes/MySQL.md#sharding-%E7%AD%96%E7%95%A5)

---

## 数据库建立索引的原则

1. 天下没有免费的午餐，使用索引是需要付出代价的

2. 对于查询中很少涉及的列或者重复值比较多的列，不要建立索引

3. 表中若有主键或者外键，一定要为其建立索引

4. 对查询中经常涉及的列，需要建立索引

5. 对于一些特殊的数据类型，不建议建立索引

   如文本字段(TXT)、图像类型字段(IMAGE)等等。因为这些字段有一些共同的特点。如长度不确定，要么很长，几个字符;要么就是空字符串。

**参考**：[数据库创建索引的原则](https://www.cnblogs.com/nwj-0613/p/4768317.html)

---

## 数据库如何建立索引

索引是快速搜索的关键。MySQL索引的建立对于MySQL的高效运行很重要。

索引分单列索引和组合索引。单列索引，即一个索引只包含单个列，一个表可以有多个单列索引。组合索引，即一个索包含多个列。

**索引类型**：

- 普通索引：最基本的索引，没有任何限制。

- 唯一索引：与前面的普通索引类似，不同的是：索引列的值必须唯一，但允许有空值。如果是组合索引，则列值的组合必须唯一。

- 主键索引：一种特殊的唯一索引，不允许有空值。一般是在建表的时候同时创建主键索引

- 组合索引：

- 前缀索引:当索引是很长的字符序列时，这个索引将会很占内存，而且会很慢，这时候就会用到前缀索引了。所谓的前缀索引就是去索引的前面几个字母作为索引

**索引的缺点**：

- 虽然索引大大提高了查询速度，同时却会降低更新表的速度。如对表进行INSERT、UPDATE和DELETE。因为更新表时，MySQL不仅要保存数据，还要保存一下索引文件。

- 建立索引会占用磁盘空间的索引文件。一般情况这个问题不太严重，但如果你在一个大表上创建了多种组合索引，索引文件会膨胀很快。

**参考**：

- [如何正确合理的建立MYSQL数据库索引](https://www.cnblogs.com/chen-lhx/p/4693887.html)

- [前缀索引](https://blog.csdn.net/ma2595162349/article/details/79449493)

---

## 聚集索引和非聚集索引的区别

聚集索引和非聚集索引的根本区别是表记录的排列顺序和与索引的排列顺序是否一致。

**参考**：[[转]聚集索引和非聚集索引的区别](https://www.jianshu.com/p/5681ebd5b0ef)

---

## 聚簇索引与非聚簇索引（也叫二级索引）

- 聚簇索引：将数据存储与索引放到了一块，找到索引也就找到了数据

- 非聚簇索引：将数据存储于索引分开结构，索引结构的叶子节点指向了数据的聚簇索引key值。比如，聚簇索引使用id来索引值，那么非聚簇索引进行索引时获得的值为id，如果想要查询的内容不是id，那么此时需要通过id进行二次索引

innodb中，在聚簇索引之上创建的索引称之为辅助索引，辅助索引访问数据总是需要二次查找，非聚簇索引都是辅助索引，像复合索引、前缀索引、唯一索引，辅助索引叶子节点存储的不再是行的物理位置，而是主键值

聚簇索引默认是主键，如果表中没有定义主键，InnoDB 会选择一个唯一的非空索引代替。如果没有这样的索引，InnoDB 会隐式定义一个主键来作为聚簇索引。InnoDB 只聚集在同一个页面中的记录。包含相邻健值的页面可能相距甚远。如果你已经设置了主键为聚簇索引，必须先删除主键，然后添加我们想要的聚簇索引，最后恢复设置主键即可。

**参考**：[聚簇索引与非聚簇索引（也叫二级索引）](https://www.jianshu.com/p/fa8192853184)

---

## 索引查找回表

MySQL中的回表查询，是二级索引无法直接查询所有列的数据，所以通过二级索引查询到聚簇索引后，再查询到想要的数据，这种通过二级索引查询出来的过程，就叫做回表。

有一个表叫 t_name ，五个字段 a、b、c、d、id，其中 id 为它的主键索引

sql1:select id from t_name where a=1 and b=2;

sql2:select c from t_name where a=1 and b=2 and d=4

针对这两句，你如何创建索引？ （联合索引，a b d）

这两句需要回表吗？（1不需要，id为主键索引，2需要）（解释参考上面“非聚簇索引”说明）

**参考**：

- [MySQL的回表查询，是什么意思？](https://segmentfault.com/q/1010000017879509)

---

## 索引优化

- 独立的列

- 多列索引

- 索引列的顺序

- 前缀索引

**参考**：[https://github.com/CyC2018/CS-Notes/blob/master/notes/MySQL.md#sharding-%E7%AD%96%E7%95%A5](https://github.com/CyC2018/CS-Notes/blob/master/notes/MySQL.md#sharding-%E7%AD%96%E7%95%A5)

---

## 索引的优点

- 大大减少了服务器需要扫描的数据行数。

- 帮助服务器避免进行排序和分组，以及避免创建临时表（B+Tree 索引是有序的，可以用于 ORDER BY 和 GROUP BY 操作。临时表主要是在排序和分组过程中创建，不需要排序和分组，也就不需要创建临时表）。

- 将随机 I/O 变为顺序 I/O（B+Tree 索引是有序的，会将相邻的数据都存储在一起）。

**参考**：[https://github.com/CyC2018/CS-Notes/blob/master/notes/MySQL.md#sharding-%E7%AD%96%E7%95%A5](https://github.com/CyC2018/CS-Notes/blob/master/notes/MySQL.md#sharding-%E7%AD%96%E7%95%A5)

---

## 数据库范式

**参考链接1对 一、二、三以及BC范式讲解的更清楚**

- 码：能唯一确定一条记录的一个/多个属性。码包括主码和候选码。任意一个候选码也能作为主键。其中主码/候选码的任意一个真子集都不能确定一条记录。

- 主属性：构成主码或候选码的属性都叫主属性！==千万不要误认为候选码的属性不是主属性！==

- 非主属性：除了主属性以外的属性都为非主属性。

- 传递依赖：如果存在A → B → C的决定关系，则C传递函数依赖于A

- 第一范式：表中的列只能含有原子性(不可再分)的值。

  第一范式就是属性不可分割，每个字段都应该是不可再拆分的。比如一个字段是姓名（NAME），在国内的话通常理解都是姓名是一个不可再拆分的单位，这时候就符合第一范式；但是在国外的话还要分为FIRST NAME和LAST NAME，这时候姓名这个字段就是还可以拆分为更小的单位的字段，就不符合第一范式了。

- 第二范式：满足第一范式；非主属性没有部分依赖

  *第二范式就是要求表中要有主键，表中其他其他字段都依赖于主键，因此第二范式只要记住主键约束就好了。*

  假定选课关系表为SelectCourse(学号, 姓名, 年龄, 课程名称, 成绩, 学分)，关键字为组合关键字(学号, 课程名称)，因为存在如下决定关系：

  (学号, 课程名称) → (姓名, 年龄, 成绩, 学分)

  这个数据库表不满足第二范式，因为存在如下决定关系：

  (课程名称) → (学分) 

  (学号) → (姓名, 年龄)

  即存在组合关键字中的字段决定非关键字的情况。

  由于不符合2NF，这个选课关系表会存在如下问题：

  1. 数据冗余：

     同一门课程由n个学生选修，"学分"就重复n-1次；同一个学生选修了m门课程，姓名和年龄就重复了m-1次。

  2. 更新异常：

     若调整了某门课程的学分，数据表中所有行的"学分"值都要更新，否则会出现同一门课程学分不同的情况。

  3. 插入异常：

     假设要开设一门新的课程，暂时还没有人选修。这样，由于还没有"学号"关键字，课程名称和学分也无法记录入数据库。

  4. 删除异常： 

     假设一批学生已经完成课程的选修，这些选修记录就应该从数据库表中删除。但是，与此同时，课程名称和学分信息也被删除了。很显然，这也会导致插入异常。

- 第三范式：满足第二范式；非主属性没有传递依赖

  *第三范式就是要求表中不能有其他表中存在的、存储相同信息的字段，通常实现是在通过外键去建立关联，因此第三范式只要记住外键约束就好了。*

  假定学生关系表为Student(学号, 姓名, 年龄, 所在学院, 学院地点, 学院电话)，关键字为单一关键字"学号"，因为存在如下决定关系：

  (学号) → (姓名, 年龄, 所在学院, 学院地点, 学院电话) 

  这个数据库是符合2NF的，但是不符合3NF，因为存在如下决定关系：

  (学号) → (所在学院) → (学院地点, 学院电话)

  即存在非关键字段"学院地点"、"学院电话"对关键字段"学号"的传递函数依赖。

- BC 范式：满足BC范式的关系将消除任何属性（主属性和非主属性）对关系键的部分函数依赖和传递函数依赖。

  参考链接3，讲的很清楚，而且有例题

**参考**：

- [数据库范式（1NF 2NF 3NF BCNF）详解一](https://www.xuebuyuan.com/3258595.html)

- [数据库三大范式通俗理解](https://www.cnblogs.com/yanggb/p/10849850.html)

- [第二、三范式与BC范式的区别](https://blog.csdn.net/doncoder/article/details/92171159)

---

## 超键，候选键、主键

- 超键(super key):在关系中能唯一标识元组的属性集称为关系模式的超键

- 候选键(candidate key):不含有多余属性的超键称为候选键

- 主键(primary key):用户选作元组标识的一个候选键程序主键

**参考**：[数据库中超键，候选键、主键的区别](https://blog.csdn.net/zoro_13/article/details/17011265)

---

## redo 和 undo 日志

innodb事务日志包括redo log和undo log。redo log是重做日志，提供前滚操作，undo log是回滚日志，提供回滚操作。

undo log不是redo log的逆向过程，其实它们都算是用来恢复的日志：

1. redo log通常是物理日志，记录的是数据页的物理修改，而不是某一行或某几行修改成怎样怎样，它用来恢复提交后的物理数据页(恢复数据页，且只能恢复到最后一次提交的位置)。

   当数据库对数据做修改的时候，需要把数据页从磁盘读到buffer pool中，然后在buffer pool中进行修改，那么这个时候buffer pool中的数据页就与磁盘上的数据页内容不一致，称buffer pool的数据页为dirty page 脏数据，如果这个时候发生非正常的DB服务重启，那么这些数据还在内存，并没有同步到磁盘文件中（注意，同步到磁盘文件是个随机IO），也就是会发生数据丢失，如果这个时候，能够再有一个文件，当buffer pool 中的data page变更结束后，把相应修改记录记录到这个文件（注意，记录日志是顺序IO），那么当DB服务发生crash的情况，恢复DB的时候，也可以根据这个文件的记录内容，重新应用到磁盘文件，数据保持一致。

   这个文件就是redo log ，用于记录 数据修改后的记录，顺序记录。它可以带来这些好处：

   当buffer pool中的dirty page 还没有刷新到磁盘的时候，发生crash，启动服务后，可通过redo log 找到需要重新刷新到磁盘文件的记录；

   buffer pool中的数据直接flush到disk file，是一个随机IO，效率较差，而把buffer pool中的数据记录到redo log，是一个顺序IO，可以提高事务提交的速度；

2. undo用来回滚行记录到某个版本。undo log一般是逻辑日志，根据每行记录进行记录。

   undo日志用于存放数据修改被修改前的值，假设修改 tba 表中 id=2的行数据，把Name=’B’ 修改为Name = ‘B2’ ，那么undo日志就会用来存放Name=’B’的记录，如果这个修改出现异常，可以使用undo日志来实现回滚操作，保证事务的一致性。

**参考**：

- [详细分析MySQL事务日志(redo log和undo log)](https://www.cnblogs.com/f-ck-need-u/archive/2018/05/08/9010872.html)

- [redo和undo日志](https://blog.csdn.net/yu757371316/article/details/81081669)

---

## 日志类型

1. 错误日志

2. 普通查询日志

3. 慢查询日志

4. binlog日志

5. undo 日志

6. redo 日志

**参考**：[mysql数据库服务日志类型](https://blog.csdn.net/qq_36441027/article/details/82055355)

---

## InnoDB 和 MyISAM 区别

- 事务：InnoDB 是事务型的，可以使用 Commit 和 Rollback 语句。

- 并发：MyISAM 只支持表级锁，而 InnoDB 还支持行级锁。

- 外键：InnoDB 支持外键。

- 备份：InnoDB 支持在线热备份。

  热备份指的是数据库在运行的过程中对数据进行备份；冷备份是在数据库停止的情况下进行备份。

- 崩溃恢复：MyISAM 崩溃后发生损坏的概率比 InnoDB 高很多，而且恢复的速度也更慢。

- 其它特性：MyISAM 支持压缩表和空间数据索引。

**参考**：[https://github.com/CyC2018/CS-Notes/blob/master/notes/MySQL.md](https://github.com/CyC2018/CS-Notes/blob/master/notes/MySQL.md)

---

## 数据库中执行查询语句经历的流程

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gcqxe9s4w0j30t309nab7.jpg)

**参考**：[https://www.nowcoder.com/test/question/done?tid=31358802&qid=53033#summary](https://www.nowcoder.com/test/question/done?tid=31358802&qid=53033#summary)

---

## 数据库慢查询

所有执行时间超过 long_query_time 的所有查询或者不使用索引的查询

**慢查询日志**:
MySQL慢查询日志记录下所有执行超过long_query_time时间的SQL语句，帮你找到执行慢的SQL，方便我们对这些SQL进行优化。

**参考**：

- [数据库优化之慢查询](https://blog.csdn.net/kikock/article/details/82285728)

- [MySQL慢查询—开启慢查询](https://www.cnblogs.com/gxj521test/p/10964795.html)

---

## MySQL与sql的区别

MySQL与oracle，Microsoft access等一样，是关系型数据库，是一个用于存储的工具。而sql与java，Python一样，是一种数据库查询语言。通常关系型数据库都是支持用sql语言进行增删改查操作的。

**参考**：[MySQL与sql的区别？小白的正确学习流程！](https://zhuanlan.zhihu.com/p/82675475)

---

## mysql 架构

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gdopcf70nwj30wp0fjwju.jpg)

- Client：MySQL向外提供的交互接口（Connectors）

- 连接池组件(Connection Pool)

- 缓存主件（Caches & Buffers）

- 查询分析器组件(Parser)

- 优化器组件（Optimizer）

- 插件式存储引擎（Pluggable Storage Engines）

- 物理文件（File System）

**参考**:

- [MySQL架构图解](https://blog.csdn.net/qq_33745102/article/details/88392220)

- [浅谈MySQL架构体系](https://www.cnblogs.com/wangjiming/p/10410904.html)

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

![image-20200822220430777](/home/qiubing/.config/Typora/typora-user-images/image-20200822220430777.png)

**参考**：

- [更新数据时，是先删除缓存再更新DB，还是先更新DB再删除缓存？](https://blog.csdn.net/qq_33999844/article/details/81531461)
- [数据库和缓存一致性分析](https://www.cnblogs.com/xmzJava/p/9778109.html)
- [Redis缓存雪崩和穿透的解决方法](https://blog.csdn.net/qq_35433716/article/details/86375506)

---

---