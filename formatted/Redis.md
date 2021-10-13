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
            - 底层实现方式：动态字符串sds 或者 long
            - 常用命令: set,get,decr,incr,mget 等。
            - String数据结构是简单的key-value类型，value其实不仅可以是String，也可以是数字。 常规key-value缓存应用； 常规计数：微博数，粉丝数等。
            - redis中所有场景中出现的字符串，基本都是由SDS(simple dynamic string)来实现的
            - ![](https://firebasestorage.googleapis.com/v0/b/firescript-577a2.appspot.com/o/imgs%2Fapp%2Flxyer%2FikHQs67TYc.png?alt=media&token=f1d9315a-3f95-4cde-bfd6-ea6930f2e857)
                - free:还剩多少空间
                - len:字符串长度
                - buf:存放的字符数组
        - Hash
            - 底层实现方式：压缩列表ziplist 或者 字典dict
                - hash中的数据项（即filed-value对）的数目超过阈值(可以设置)时，或者当hash中插入的任意一个value的长度超过了64字节的时候，ziplist的结构转为字典dict
                    - Redis的hash之所以这样设计，是因为当ziplist变得很大的时候，它有如下几个缺点：
                        - 每次插入或修改引发的realloc操作会有更大的概率造成内存拷贝，从而降低性能。
                        - 一旦发生内存拷贝，内存拷贝的成本也相应增加，因为要拷贝更大的一块数据。
                        - 当ziplist数据项过多的时候，在它上面查找指定的数据项就会性能变得很低，因为ziplist上的查找需要进行遍历。
            - 常用命令： hget,hset,hgetall 等。
            - Hash 是一个 string 类型的 ﬁeld 和 value 的映射表，hash 特别适合用于存储对象，后续操作的时候，你可以直接仅 仅修改这个对象中的某个字段的值。 比如我们可以Hash数据结构来存储用户信息，商品信息等等。
        - List
            - Redis3.2及之后的底层实现方式：quicklist
                - quicklist是一个双向链表，而且是一个基于ziplist的双向链表，quicklist的每个节点都是一个ziplist，结合了双向链表和ziplist的优点
            - 常用命令: lpush,rpush,lpop,rpop,lrange等
            - list 就是链表，[Redis](<Redis.md>) list 的应用场景非常多，也是[Redis](<Redis.md>)最重要的数据结构之一，比如微博的关注列表，粉丝列表， 消息列表等功能都可以用[Redis](<Redis.md>)的 list 结构来实现。
            - [Redis](<Redis.md>) list 的实现为一个双向链表，即可以支持反向查找和遍历，更方便操作，不过带来了部分额外的内存开销。
        - Set
            - 底层实现方式：有序整数集合intset 或者 字典dict
                - 满足下面这样两个条件的时候，Redis 就采用整数集合intset来实现set这种数据类型：
                    - 存储的数据都是整数
                    - 存储的数据元素个数小于512个
            - 常用命令： sadd,spop,smembers,sunion 等
            - set 对外提供的功能与list类似是一个列表的功能，特殊之处在于 set 是可以自动排重的。当你需要存储一个列表数据，又不希望出现重复数据时，set是一个很好的选择，并且set提供了判断某个成员是否在 一个set集合内的重要接口，这个也是list所不能提供的。可以基于 set 轻易实现交集、并集、差集的操作。
        - Sorted Set
            - 底层实现方式：压缩列表ziplist 或者 zset
                - 满足下面这两个条件的时候，Redis就使用压缩列表ziplist实现sorted set：
                    - 集合中每个数据的大小都要小于 64 字节
                    - 元素个数要小于 128 个，也就是ziplist数据项小于256个
                - 当不能同时满足这两个条件的时候，Redis 就使用zset来实现sorted set，这个zset包含一个dict + 一个skiplist
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
- 大key问题
    - 危害
        - 内存空间不均匀
        - 操作耗时，存在阻塞风险
        - 网络阻塞，每次获取大key产生的网络流量较大
        - 大key相关的删除或者自动过期时，操作比较耗时
        - 极端情况下，会造成主从复制异常
        - 导致倾斜：会导致这个实例的数据量增加，内存资源消耗也相应增加。实例的处理压力就会增大，速度变慢，甚至还可能会引起这个实例的内存资源耗尽，从而崩溃。
    - 解决办法
        - 对象需要每次都整存整取
            - 可以尝试将对象分拆成几个key-value， 使用multiGet获取值，这样分拆的意义在于分拆单次操作的压力，将操作压力平摊到多个redis实例中，降低对单个redis的IO影响；
        - 该对象每次只需要存取部分数据：拆分
            - 可以像第一种做法一样，分拆成几个key-value， 也可以将这个存储在一个hash中，每个field代表一个具体的属性，使用hget,hmget来获取部分的value，使用hset，hmset来更新部分属性
        - 对长文本的存储场景可以考虑使用其他文档型数据库替换：MongoDB等
        - 对于量少的大key 可以考虑本地cache
- Redis过期删除
    - 过期时间的判定：在Redis内部，每当我们设置一个键的过期时间时，Redis就会将该键带上过期时间存放到一个**过期字典**中。当我们查询一个键时，Redis便首先检查该键是否存在过期字典中，如果存在，那就获取其过期时间。然后将过期时间和当前系统时间进行比对，比系统时间大，那就没有过期；反之判定该键过期。
    - 过期删除策略
        - 定时删除
            - 在设置某个key 的过期时间同时，我们创建一个定时器，让定时器在该过期时间到来时，立即执行对其进行删除的操作。
                - 优点：定时删除对内存是最友好的，能够保存内存的key一旦过期就能立即从内存中删除。
                - 缺点：对CPU最不友好，在过期键比较多的时候，删除过期键会占用一部分 CPU 时间，对服务器的响应时间和吞吐量造成影响。
        - 惰性删除
            - 设置该key 过期时间后，我们不去管它，当需要该key时，我们在检查其是否过期，如果过期，我们就删掉它，反之返回该key。
                - 优点：对 CPU友好，我们只会在使用该键时才会进行过期检查，对于很多用不到的key不用浪费时间进行过期检查。
                - 缺点：对内存不友好，如果一个键已经过期，但是一直没有使用，那么该键就会一直存在内存中，如果数据库中有很多这种使用不到的过期键，这些键便永远不会被删除，内存永远不会释放。从而造成内存泄漏。
        - 定期删除
            - 每隔一段时间，我们就对一些key进行检查，删除里面过期的key。
                - 优点：可以通过限制删除操作执行的时长和频率来减少删除操作对 CPU 的影响。另外定期删除，也能有效释放过期键占用的内存。
                - 缺点：难以确定删除操作执行的时长和频率。
    - Redis的过期删除策略就是：惰性删除和定期删除两种策略配合使用
        - **惰性删除**：Redis的惰性删除策略由 db.c/expireIfNeeded 函数实现，所有键读写命令执行之前都会调用 expireIfNeeded 函数对其进行检查，如果过期，则删除该键，然后执行键不存在的操作；未过期则不作操作，继续执行原有的命令。
        - **定期删除**：由redis.c/activeExpireCycle 函数实现，函数以一定的频率运行，每次运行时，都从一定数量的数据库中取出一定数量的随机键进行检查，并删除其中的过期键。
    - 通过过期删除策略，对于某些永远使用不到的键，并且多次定期删除也没选定到并删除，那么这些键同样会一直驻留在内存中，又或者在Redis中存入了大量的键，这些操作可能会导致Redis内存不够用，这时候就需要Redis的内存淘汰策略了。
        - 设置Redis最大内存
        - 设置内存淘汰方式
- [图解redis五种数据结构底层实现(动图哦)](https://i6448038.github.io/2019/12/01/redis-data-struct/)
- Redis和Memcached的区别，都什么时候使用？
    - memcache使用预分配池管理，会提前把内存分为多个slab，slab又分成多个不等大小的chunk，chunk从最小的开始，根据增长因子增长内存大小。redis更适合做数据存储，memcache更适合做缓存，memcache在存储速度方面也会比redis这种申请内存的方式来的快。
    - 从数据一致性来说，memcache提供了cas命令，可以保证多个并发访问操作同一份数据的一致性问题。 redis是串行操作，所以不用考虑数据一致性的问题。
    - 从IO角度来说，选用的I/O多路复用模型，虽然单线程不用考虑锁等问题，但是还要执行kv数据之外的一些排序、聚合功能，复杂度比较高。memcache也选用非阻塞的I/O多路复用模型，速度更快一些。
    - 从线程角度来说，memcahce使用多线程，主线程listen，多个worker子线程执行读写，可能会出现锁冲突。redis是单线程的，这样虽然不用考虑锁对插入修改数据造成的时间的影响，但是无法利用多核提高整体的吞吐量，只能选择多开redis来解决。
    - 从集群方面来说，redis天然支持高可用集群，支持主从，而memcache需要自己实现类似一致性hash的负载均衡算法才能解决集群的问题，扩展性比较低。
    - redis确实比memcache功能更全，集成更方便，但是memcache相比redis在内存、线程、IO角度来说都有一定的优势，可以利用cpu提高机器性能，在不考虑扩展性和持久性的访问频繁的情况下，只存储kv格式的数据，建议使用memcache，memcache更像是个缓存，而redis更偏向与一个存储数据的系统。但是，觉得不要拿redis当数据库用！！！
- 发现热点数据
    - 人为预测&业务预测（秒杀）
    - 系统推算：使用Redis的内存淘汰策略LFU，之后使用Redis的hotkeys指令查询热点key

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

