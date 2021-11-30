- ____ — via [【接入必看】业务方使用缓存必看 - 数科技术平台 - 主页面](https://cf.jd.com/pages/viewpage.action?pageId=350685726) [[+Roam]]
    - ## **一、R2M缓存适用范围**
        - ### **1、什么是R2M？**
            - 一句话概括：R2M是基于开源的Redis cluster(Redis 3.0以上版本)构建的高性能分布式缓存系统。
        - ### **2、Redis cluster特性**
            - 速度快：因为是基于Redis cluster实现，所以数据都是在内存中操作，并且使用了单线程的架构；
            - 分布式：保证了故障发现和故障自动failover，提供了高可用，以及水平扩展的能力；
        - ### **3、可支持业务场景**
            - （1）缓存数据：合理地利用R2M保存缓存数据，可以有效地加快数据访问，降低后端数据源的压力；
            - （2）计数器：合理利用incr、getset等命令以及lua脚本，实现计数、限制访问等功能；
            - （3）排行榜：合理地使用列表、有序集合，可以方便地构建排行榜系统；
            - （4）签到、活跃用户、标签：此类业务场景可以利用bitmap数据类型，大大节省内存空间占用；
            - （5）地理定位GEO：支持基于地理位置信息而实现的业务场景，如附近位置等；
        - ### **4、不适用业务场景**
            - （1）以数据冷热的角度，适合存放操作频繁的数据，不适合存放冷数据
            - （2）以数据规模的角度，因数据是存放在内存中，当数据量非常庞大的时候，完全依赖缓存会产生较高的经济成本，另外集群风险会有一定升高，运维也会变得困难，此时就要考虑更好的架构设计或数据优化方案；
            - （3）R2M缓存不进行数据持久化，数据不要强依赖R2M缓存（业务架构需要自行设计二级数据存储）
            - 以下场景存在缓存数据丢失的可能性：
                - 关闭AOF持久化的集群，由于R2M分片默认为一主一从结构（主从不在同一台主机），极端情况下，主从同时宕机，则该分片数据丢失；
                - 开启从节点AOF持久化的集群，为了兼顾性能和数据安全性，默认采用系统控制刷盘策略，大概每30秒钟将增量缓存数据刷到磁盘，当机器故障/突然断电时，最近30秒钟左右产生的数据可能会丢失；
                - 当脑裂(网络分区)发生时，可能导致少数节点所在分区中写入的数据后续无法再同步到多数节点所在分区，导致数据丢失；
                - 主节点故障/挂掉时，因redis cluster自身节点宕机判定及新主节点选举机制，造成主从Failover存在15秒左右的时间周期，期间客户端重试失败，导致数据丢失；
                - 主节点故障/挂掉时，极少量数据还未同步到从节点，从节点进行了failover成为新的主节点，导致数据丢失；
            - 综上所述：集群单节点内存不超过4000MB、集群节点数量（主从相加）不超过150、集群单节点TPS不超过40k/s，我们认为是一个相对较好的状态，因此当集群大部分情况下已经突破这个值，建议使用方开始进行架构、数据等层面的优化，而不是无底洞使用。
    - ## **二、R2M缓存使用规范**
        - ### **1、数据**
            - (1)避免大 key 操作
                - 参考：根据测试结果，value在超过1KB后性能开始下降，超过10KB后性能下降明显，出现拐点
                - ![](https://firebasestorage.googleapis.com/v0/b/firescript-577a2.appspot.com/o/imgs%2Fapp%2Flxyer%2FRgpS6gQFuT.png?alt=media&token=c8da68d0-6aa6-4be5-8519-f194ebe44ede)
            - (2)避免热点key、内存倾斜
                - 避免整个应用的缓存数据都集中在几个key上(如：整个缓存只有一个或几个非常大的 list 或hash map)，业务方最好事先对大list或大key进行拆分，这种热点key、内存倾斜势必造成个别节点压力过大。
            - （3）平衡数据容量与查询命中率
                - 使用方应进行合理设计，在业务需求、缓存力度、缓存策略等各个方面权衡，尽可能地将缓存数据聚焦在高频访问且时效性要求相对不高的热点业务上，以保障缓存数据的有效性，实现数据容量与查询命中率的平衡。
                - 对于时效性很高（或缓存空间有限），内容跨度很大（或访问很随机），并且访问量不高的应用来说，缓存命中率可能长期很低，可能预热后的缓存还没来得被访问就已经过期了
        - ### **2、配置**
            - （1）合理选择数据淘汰策略
                - 数据淘汰策略，主要是指当缓存容量用满没来得及扩容的时候如何处理的策略。
                    - noeviction：不删除数据，将不能进行新的写入，扩容后恢复正常。
                    - allkeys-lru：选择最近最少使用的key进行删除来保证可以写入新的数据。丢弃数据来换取可用性。
                    - volatile-lru：从设置了过期时间的key中选择最近最少使用的key进行删除来保证新的写入，如果没有设置过期时间的key把容量占满了，同样将不能进行新的写入，扩容后恢复正常。
                    - volatile-ttl：从设置了过期时间的key中选择即将过期的key进行删除来保证新的写入，如果没有设置过期时间的key把容量占满了，同样将不能进行新的写入，扩容后恢复正常。
            - （2）合理设置连接池
            - （3）合理设置超时时间
            - （4）客户端版本
            - （5）zookeeper版本
            - （6）监控开关及阈值
        - **3、高阶使用**
            - （1）跨节点操作
            - （2）关于事务
            - （3）关于pipeline使用注意事项
            - （4）数据遍历