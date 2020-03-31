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

## 写命令

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
