- ____ — via [个人吐血系列-总结[Redis](<Redis.md>) - 掘金](https://juejin.cn/post/6844904132868833293?utm_source=gold_browser_extension[heading-8](<heading-8.md>)) [+Roam](<+Roam.md>)
    - 使用[Redis](<Redis.md>)有哪些好处
        - 「速度快」，因为数据存在内存中，类似于HashMap，HashMap的优势就是查找和操作的时间复杂度都是O(1)
        - 支持丰富数据类型，支持「string，list，set，sorted set，hash」
        - 「支持事务」，操作都是原子性，所谓的原子性就是对数据的更改要么全部执行，要么全部不执行
        - 丰富的特性：「可用于缓存，消息，按key设置过期时间，过期后将会自动删除」
    - [Redis](<Redis.md>)的[线程](<线程.md>)模型：
        - [Redis](<Redis.md>) 内部使用文件事件处理器 file event handler，这个文件事件处理器是「单[线程](<线程.md>)」的，所以 [Redis](<Redis.md>) 才叫做「单[线程](<线程.md>)的模型」。它采用 「IO 多路复用机制」同时监听多个 socket，根据 socket 上的事件来「选择对应的事件处理器」进行处理。
    - 文件事件处理器的结构包含 4 个部分：
        - 多个 socket
        - IO多路复用程序
        - 文件事件分派器
        - 事件处理器（连接应答处理器、命令请求处理器、命令回复处理器）
    - [Redis](<Redis.md>)常见性能问题和解决方案
        - Master最好不要做任何持久化工作，如RDB内存快照和AOF日志文件
        - 如果数据比较重要，某个Slave开启AOF备份数据，策略设置为每秒同步一次
        - 为了主从复制的速度和连接的稳定性，Master和Slave最好在同一个局域网内
        - 尽量避免在压力很大的主库上增加从库
        - 主从复制不要用图状结构，用单向链表结构更为稳定，即：Master <- Slave1 <- Slave2 <- Slave3...
    - [Redis](<Redis.md>)常见数据结构以及使用场景分析
        - String
            - 常用命令: set,get,decr,incr,mget 等。
            - String数据结构是简单的key-value类型，value其实不仅可以是String，也可以是数字。 常规key-value缓存应用； 常规计数：微博数，粉丝数等。
            - redis中所有场景中出现的字符串，基本都是由SDS(simple dynamic string)来实现的
            - ![](https://firebasestorage.googleapis.com/v0/b/firescript-577a2.appspot.com/o/imgs%2Fapp%2Flxyer%2FikHQs67TYc.png?alt=media&token=f1d9315a-3f95-4cde-bfd6-ea6930f2e857)
                - free:还剩多少空间
                - len:字符串长度
                - buf:存放的字符数组
        - Hash
            - 常用命令： hget,hset,hgetall 等。
            - Hash 是一个 string 类型的 ﬁeld 和 value 的映射表，hash 特别适合用于存储对象，后续操作的时候，你可以直接仅 仅修改这个对象中的某个字段的值。 比如我们可以Hash数据结构来存储用户信息，商品信息等等。
        - List
            - 常用命令: lpush,rpush,lpop,rpop,lrange等
            - list 就是链表，[Redis](<Redis.md>) list 的应用场景非常多，也是[Redis](<Redis.md>)最重要的数据结构之一，比如微博的关注列表，粉丝列表， 消息列表等功能都可以用[Redis](<Redis.md>)的 list 结构来实现。
            - [Redis](<Redis.md>) list 的实现为一个双向链表，即可以支持反向查找和遍历，更方便操作，不过带来了部分额外的内存开销。
        - Set
            - 常用命令： sadd,spop,smembers,sunion 等
            - set 对外提供的功能与list类似是一个列表的功能，特殊之处在于 set 是可以自动排重的。当你需要存储一个列表数据，又不希望出现重复数据时，set是一个很好的选择，并且set提供了判断某个成员是否在 一个set集合内的重要接口，这个也是list所不能提供的。可以基于 set 轻易实现交集、并集、差集的操作。
        - Sorted Set
            - 常用命令： zadd,zrange,zrem,zcard等
            - 和set相比，sorted set增加了一个权重参数score，使得集合中的元素能够按score进行有序排列。
        - 定期删除+惰性删除
            - 定期删除：[Redis](<Redis.md>)默认是每隔 100ms 就随机抽取一些设置了过期时间的key，检查其是否过期，如果过期就删 除。注意这里是随机抽取的。为什么要随机呢？你想一想假如 [Redis](<Redis.md>) 存了几十万个 key ，每隔100ms就遍历所 有的设置过期时间的 key 的话，就会给 CPU 带来很大的负载！
            - 惰性删除 ：定期删除可能会导致很多过期 key 到了时间并没有被删除掉。所以就有了惰性删除。假如你的过期 key，靠定期删除没有被删除掉，还停留在内存里，除非你的系统去查一下那个 key，才会被[Redis](<Redis.md>)给删除掉。这 就是所谓的惰性删除，也是够懒的哈！
        - Mysql有2000万数据，[Redis](<Redis.md>)只存20万，如何保证[Redis](<Redis.md>)中的数据都是热点数据
            - [Redis](<Redis.md>) 内存数据集大小上升到一定大小的时候，就会施行数据淘汰策略。[Redis](<Redis.md>) 提供8种数据淘汰策略：
                - voltile-lru：从已设置过期时间的数据集（server.db[i].expires）中挑选最近最少使用的数据淘汰
                - volatile-ttl：从已设置过期时间的数据集（server.db[i].expires）中挑选将要过期的数据淘汰
                - volatile-random：从已设置过期时间的数据集（server.db[i].expires）中任意选择数据淘汰
                - allkeys-lru：从数据集（server.db[i].dict）中挑选最近最少使用的数据淘汰
                - allkeys-random：从数据集（server.db[i].dict）中任意选择数据淘汰
                - no-enviction（驱逐）：禁止驱逐数据
        - [Redis](<Redis.md>)持久化机制
            - [Redis](<Redis.md>)的一种持久化方式叫「快照（snapshotting，RDB）」,另一种方式是「只追加文件（append-only ﬁle,AOF）」
                - 快照（snapshotting）持久化（RDB）（默认方式）
                    - [Redis](<Redis.md>)可以通过创建快照来获得存储在内存里面的数据在某个时间点上的副本。[Redis](<Redis.md>)创建快照之后，可以对快照进行 备份，可以将快照复制到其他服务器从而创建具有相同数据的服务器副本（[Redis](<Redis.md>)主从结构，主要用来提高[Redis](<Redis.md>)性 能），还可以将快照留在原地以便重启服务器的时候使用。
                - AOF（append-only file）持久化
                    - 三种不同的 AOF 持久化方式
                        - appendfsync always #每次有数据修改发生时都会写入AOF文件,这样会严重降低[Redis](<Redis.md>)的速度
                        - appendfsync everysec  #每秒钟同步一次，显示地将多个写命令同步到硬盘
                        - appendfsync no  #让操作系统决定何时进行同步
                - AOF重写
                    - AOF重写可以产生一个新的AOF文件，这个新的AOF文件和原有的AOF文件所保存的数据库状态一样，「但体积更小」。
            - 是否使用[Redis](<Redis.md>)集群，集群的原理是什么
                - [Redis](<Redis.md>) Sentinel着眼于高可用，在master宕机时会自动将slave提升为master，继续提供服务。
                - [Redis](<Redis.md>) Cluster着眼于扩展性，在单个[Redis](<Redis.md>)内存不足时，使用Cluster进行分片存储。
            - [Redis](<Redis.md>)的同步机制了解吗？
                - 主从同步。第一次同步时，「主节点做一次bgsave」，并同时将后续修改操作记录到「内存buffer」，待完成后「将rdb文件全量同步到复制节点」，复制节点接受完成后「将rdb镜像加载到内存」。加载完成后，再通知主节点「将期间修改的操作记录同步到复制节点进行重放」就完成了同步过程。
            - [Redis](<Redis.md>)常见的性能问题都有哪些？如何解决？
                - Master写内存快照，save命令调度rdbSave函数，会阻塞主[线程](<线程.md>)的工作，当快照比较大时对性能影响是非常大的，会间断性暂停服务，所以Master最好不要写内存快照。
                - Master AOF持久化，如果不重写AOF文件，这个持久化方式对性能的影响是最小的，但是AOF文件会不断增大，AOF文件过大会影响Master重启的恢复速度。Master最好不要做任何持久化工作，包括内存快照和AOF日志文件，特别是不要启用内存快照做持久化,如果数据比较关键，某个Slave开启AOF备份数据，策略为每秒同步一次。
                - Master调用BGREWRITEAOF重写AOF文件，AOF在重写的时候会占大量的CPU和内存资源，导致服务load过高，出现短暂服务暂停现象。
                - [Redis](<Redis.md>)主从复制的性能问题，为了主从复制的速度和连接的稳定性，Slave和Master最好在同一个局域网内
            - [Redis](<Redis.md>) 通过 MULTI、EXEC、WATCH 等命令来实现事务(transaction)功能。事务提供了一种「将多个命令请求打包，然 后一次性、按顺序地执行多个命令的机制，并且在事务执行期间，服务器不会中断事务而改去执行其他客户端的命令 请求，它会将事务中的所有命令都执行完毕」，然后才去处理其他客户端的命令请求。
- 持久化方式RDB和AOF
    - 二者的区别
        - RDB持久化是指在指定的时间间隔内将内存中的数据集快照写入磁盘，实际操作过程是fork一个子进程，先将数据集写入临时文件，写入成功后，再替换之前的文件，用二进制压缩存储。
        - AOF持久化以日志的形式记录服务器所处理的每一个写、删除操作，查询操作不会记录，以文本的方式记录，可以打开文件看到详细的操作记录。
    - RDB
        - 优点：
            - 一旦采用该方式，那么你的整个Redis数据库将只包含一个文件，这对于文件备份而言是非常完美的
            - 对于灾难恢复而言，RDB是非常不错的选择。因为我们可以非常轻松的将一个单独的文件压缩后再转移到其它存储介质上。
            - 性能最大化。对于Redis的服务进程而言，在开始持久化时，它唯一需要做的只是fork出子进程，之后再由子进程完成这些持久化的工作，这样就可以极大的避免服务进程执行IO操作了。
            - 相比于AOF机制，如果数据集很大，RDB的启动效率会更高。
        - 缺点：
            - 如果你想保证数据的高可用性，即最大限度的避免数据丢失，那么RDB将不是一个很好的选择。因为系统一旦在定时持久化之前出现宕机现象，此前没有来得及写入磁盘的数据都将丢失。
            - 由于RDB是通过fork子进程来协助完成数据持久化工作的，因此，如果当数据集较大时，可能会导致整个服务器停止服务几百毫秒，甚至是1秒钟。
        - AOF
            - 优点：
                - 该机制可以带来更高的数据安全性，即数据持久性
                - 由于该机制对日志文件的写入操作采用的是append模式，因此在写入过程中即使出现宕机现象，也不会破坏日志文件中已经存在的内容
                - 如果日志过大，Redis可以自动启用rewrite机制。即Redis以append模式不断的将修改数据写入到老的磁盘文件中，同时Redis还会创建一个新的文件用于记录此期间有哪些修改命令被执行。因此在进行rewrite切换时可以更好的保证数据安全性。
                - AOF包含一个格式清晰、易于理解的日志文件用于记录所有的修改操作
            - 缺点：
                - 对于相同数量的数据集而言，AOF文件通常要大于RDB文件。RDB 在恢复大数据集时的速度比 AOF 的恢复速度要快。
                - 根据同步策略的不同，AOF在运行效率上往往会慢于RDB。总之，每秒同步策略的效率是比较高的，同步禁用策略的效率和RDB一样高效。
    - 常用配置
        - RDB持久化配置
            - save 900 1 #在900秒(15分钟)之后，如果至少有1个key发生变化，则dump内存快照。
            - save 300 10 #在300秒(5分钟)之后，如果至少有10个key发生变化，则dump内存快照。
            - save 60 10000 #在60秒(1分钟)之后，如果至少有10000个key发生变化，则dump内存快照。
        - AOF持久化配置
            - appendfsync always #每次有数据修改发生时都会写入AOF文件。
            - appendfsync everysec #每秒钟同步一次，该策略为AOF的缺省策略。
            - appendfsync no #从不同步。高效但是数据不会被持久化。

# Backlinks
## [August 4th, 2020](<August 4th, 2020.md>)
- 随机字符串后台收到后放入到[Redis](<Redis.md>)中

- 随机字符串后台收到后放入到[Redis](<Redis.md>)中,如果发现[Redis](<Redis.md>)中

- 随机字符串后台收到后放入到[Redis](<Redis.md>)中,如果发现[Redis](<Redis.md>)中已有值,则判断此次访问无效,可以保证参数访问的一次性有效性.缺点为这个[Redis](<Redis.md>)中

- [最佳实践](<最佳实践.md>)结合timestamp和nonce,[Redis](<Redis.md>)中

## [CV](<CV.md>)
- # [Redis](<Redis.md>)

- 难以支持在线扩容，[Redis](<Redis.md>)的

- 多个哨兵可以监控同一个[Redis](<Redis.md>)，

- 同样也继承了主从模式难以在线扩容的缺点，[Redis](<Redis.md>)的

- 哨兵模式解决了主从复制不能自动故障转移，达不到高可用的问题，但还是存在难以在线扩容，[Redis](<Redis.md>)容

- 哨兵模式解决了主从复制不能自动故障转移，达不到高可用的问题，但还是存在难以在线扩容，[Redis](<Redis.md>)容量受限于单机配置的问题。Cluster模式实现了[Redis](<Redis.md>)的

- 所有的[Redis](<Redis.md>)节

- 客户端与[Redis](<Redis.md>)节

- 在[Redis](<Redis.md>)的

- 当我们存取key的时候，[Redis](<Redis.md>)会

- [Redis](<Redis.md>)与

- [Redis](<Redis.md>)不

- [Redis](<Redis.md>)支

- [Redis](<Redis.md>)支

- [Redis](<Redis.md>)中

- [Redis](<Redis.md>)中，并不是所有的数据都一直存储在内存中的。这是和Memcached相比一个最大的区别。[Redis](<Redis.md>)只

- [Redis](<Redis.md>)中，并不是所有的数据都一直存储在内存中的。这是和Memcached相比一个最大的区别。[Redis](<Redis.md>)只会缓存所有的 key的信息，如果[Redis](<Redis.md>)发

- [Redis](<Redis.md>)中，并不是所有的数据都一直存储在内存中的。这是和Memcached相比一个最大的区别。[Redis](<Redis.md>)只会缓存所有的 key的信息，如果[Redis](<Redis.md>)发现内存的使用量超过了某一个阀值，将触发swap的操作，[Redis](<Redis.md>)根

- [Redis](<Redis.md>)中，并不是所有的数据都一直存储在内存中的。这是和Memcached相比一个最大的区别。[Redis](<Redis.md>)只会缓存所有的 key的信息，如果[Redis](<Redis.md>)发现内存的使用量超过了某一个阀值，将触发swap的操作，[Redis](<Redis.md>)根据“swappability = age*log(size_in_memory)”计 算出哪些key对应的value需要swap到磁盘。然后再将这些key对应的value持久化到磁盘中，同时在内存中清除。这种特性使得[Redis](<Redis.md>)可

- Memcached提供了cas命令，可以保证多个并发访问操作同一份数据的一致性问题。 [Redis](<Redis.md>)没

- Memcached提供了cas命令，可以保证多个并发访问操作同一份数据的一致性问题。 [Redis](<Redis.md>)没有提供cas 命令，并不能保证这点，不过[Redis](<Redis.md>)提

- Memcached通过使用多个内核实现多[线程](<线程.md>)体系结构。 因此，对于存储更大的数据集，Memcached的性能要优于[Redis](<Redis.md>)。

- Memcached通过使用多个内核实现多[线程](<线程.md>)体系结构。 因此，对于存储更大的数据集，Memcached的性能要优于[Redis](<Redis.md>)。[Redis](<Redis.md>)使

- [Redis](<Redis.md>)各

## [October 24th, 2020](<October 24th, 2020.md>)
- [Redis](<Redis.md>)

## [Redis](<Redis.md>)
- ____ — via [个人吐血系列-总结[Redis](<Redis.md>)

- 使用[Redis](<Redis.md>)有

- [Redis](<Redis.md>)的

- [Redis](<Redis.md>)

- [Redis](<Redis.md>) 内部使用文件事件处理器 file event handler，这个文件事件处理器是「单[线程](<线程.md>)」的，所以 [Redis](<Redis.md>)

- [Redis](<Redis.md>)常

- [Redis](<Redis.md>)常

- list 就是链表，[Redis](<Redis.md>)

- list 就是链表，[Redis](<Redis.md>) list 的应用场景非常多，也是[Redis](<Redis.md>)最

- list 就是链表，[Redis](<Redis.md>) list 的应用场景非常多，也是[Redis](<Redis.md>)最重要的数据结构之一，比如微博的关注列表，粉丝列表， 消息列表等功能都可以用[Redis](<Redis.md>)的

- [Redis](<Redis.md>)

- 定期删除：[Redis](<Redis.md>)默

- 定期删除：[Redis](<Redis.md>)默认是每隔 100ms 就随机抽取一些设置了过期时间的key，检查其是否过期，如果过期就删 除。注意这里是随机抽取的。为什么要随机呢？你想一想假如 [Redis](<Redis.md>)

- 惰性删除 ：定期删除可能会导致很多过期 key 到了时间并没有被删除掉。所以就有了惰性删除。假如你的过期 key，靠定期删除没有被删除掉，还停留在内存里，除非你的系统去查一下那个 key，才会被[Redis](<Redis.md>)给

- Mysql有2000万数据，[Redis](<Redis.md>)只

- Mysql有2000万数据，[Redis](<Redis.md>)只存20万，如何保证[Redis](<Redis.md>)中

- [Redis](<Redis.md>)

- [Redis](<Redis.md>) 内存数据集大小上升到一定大小的时候，就会施行数据淘汰策略。[Redis](<Redis.md>)

- [Redis](<Redis.md>)持

- [Redis](<Redis.md>)的

- [Redis](<Redis.md>)可

- [Redis](<Redis.md>)可以通过创建快照来获得存储在内存里面的数据在某个时间点上的副本。[Redis](<Redis.md>)创

- [Redis](<Redis.md>)可以通过创建快照来获得存储在内存里面的数据在某个时间点上的副本。[Redis](<Redis.md>)创建快照之后，可以对快照进行 备份，可以将快照复制到其他服务器从而创建具有相同数据的服务器副本（[Redis](<Redis.md>)主

- [Redis](<Redis.md>)可以通过创建快照来获得存储在内存里面的数据在某个时间点上的副本。[Redis](<Redis.md>)创建快照之后，可以对快照进行 备份，可以将快照复制到其他服务器从而创建具有相同数据的服务器副本（[Redis](<Redis.md>)主从结构，主要用来提高[Redis](<Redis.md>)性

- appendfsync always #每次有数据修改发生时都会写入AOF文件,这样会严重降低[Redis](<Redis.md>)的

- 是否使用[Redis](<Redis.md>)集

- [Redis](<Redis.md>)

- [Redis](<Redis.md>)

- [Redis](<Redis.md>) Cluster着眼于扩展性，在单个[Redis](<Redis.md>)内

- [Redis](<Redis.md>)的

- [Redis](<Redis.md>)常

- [Redis](<Redis.md>)主

- [Redis](<Redis.md>)

## [幂等性](<幂等性.md>)
- 在[Redis](<Redis.md>)中

- 在[Redis](<Redis.md>)中保存唯一id,每次插入前判断[Redis](<Redis.md>)中

## [延迟任务](<延迟任务.md>)
- [Redis](<Redis.md>)

- [Redis](<Redis.md>)

- [Redis](<Redis.md>)

