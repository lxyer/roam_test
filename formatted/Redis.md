- [ ] https://juejin.im/post/5e9d6a9ff265da47e34c0e8a?utm_source=gold_browser_extension[heading-8](<heading-8.md>)
- 不过与传统数据库不同的是redis的数据库是存在内存中，所以读写速度非常快，因此redis被广泛应用于[缓存](<缓存.md>)方向
- 特性
    - [支持事务](<支持事务.md>)
    - 持久化
    - LUA脚本
    - [LRU](<LRU.md>)驱动事件
    - 多种[集群](<集群.md>)
- 数据结构
    - String
        - 常用命令: set,get,decr,incr,mget 等。
        - String数据结构是简单的key-value类型，value其实不仅可以是String，也可以是数字。 常规key-value缓存应用； 常规计数：微博数，粉丝数等。
    - [Hash](<Hash.md>)
        - 常用命令： hget,hset,hgetall 等。
        - Hash 是一个 string 类型的 ﬁeld 和 value 的映射表，hash 特别适合用于存储对象，后续操作的时候，你可以直接仅 仅修改这个对象中的某个字段的值。 比如我们可以Hash数据结构来存储用户信息，商品信息等等。
    - List
        - 常用命令: lpush,rpush,lpop,rpop,lrange等
        - list 就是链表，Redis list 的应用场景非常多，也是Redis最重要的数据结构之一，比如微博的关注列表，粉丝列表， 消息列表等功能都可以用Redis的 list 结构来实现。
    - [Set](<Set.md>)
        - 常用命令： sadd,spop,smembers,sunion 等
        - set 对外提供的功能与list类似是一个列表的功能，特殊之处在于 set 是可以自动排重的。
    - [Sorted Set](<Sorted Set.md>)
        - 常用命令： zadd,zrange,zrem,zcard等
        - 和set相比，[Sorted Set](<Sorted Set.md>)增加了一个[权重](<权重.md>)参数score，使得集合中的元素能够按score进行有序排列。
- 删除策略
    - 定期删除：redis默认是每隔 100ms 就随机抽取一些设置了过期时间的key，检查其是否过期，如果过期就删除。
    - 惰性删除 ：定期删除可能会导致很多过期 key 到了时间并没有被删除掉。所以就有了惰性删除。假如你的过期 key，靠定期删除没有被删除掉，还停留在内存里，除非你的系统去查一下那个 key，才会被redis给删除掉。
- 

# Backlinks
## [幂等性](<幂等性.md>)
- 在[Redis](<Redis.md>)中

- 在[Redis](<Redis.md>)中保存唯一id,每次插入前判断[Redis](<Redis.md>)中

## [延迟任务](<延迟任务.md>)
- [Redis](<Redis.md>)

