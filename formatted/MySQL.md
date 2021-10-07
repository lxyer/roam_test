- [数据库优化](<数据库优化.md>)
    - [索引](<索引.md>)
        - 1. [普通索引](<普通索引.md>): 即针对数据库表创建索引
        - 4. [组合索引](<组合索引.md>): 为了进一步榨取 MySQL 的效率，就要考虑建立组合索引。 即将数据库表中的多个字段联合起来作为一个组合索引。
    - 减少[回表](<回表.md>)
        - 回表:通过条件查询到符合要求的id列表,在使用id主键去表中查询结果集,如果回表次数过多,需要优化SQL
- 锁
    - [全局锁](<全局锁.md>)
        - MySQL提供了一个加全局读锁的方法，命令是 Flush tables with read lock ([FTWRL](<FTWRL.md>))。当你需要让整个库处于只读状态的时候，可以使用这个命令，之后其他[线程](<线程.md>)的以下语句会被阻塞
        - 全局锁的典型使用场景是，做全库[逻辑备份](<逻辑备份.md>)
        - [mysqldump](<mysqldump.md>)使用参数–single-transaction的时候，导数据之前就会启动一个事务，来确保拿到一致性视图
        - single-transaction方法只适用于所有的表使用事务引擎的库。如果有的表使用了不支持事务的引擎，那么备份就只能通过[FTWRL](<FTWRL.md>)方法
        - set global readonly=true
        - 确实readonly方式也可以让全库进入只读状态，但我还是会建议你用FTWRL方式，主要有两个原因
            - 一是，在有些系统中，readonly的值会被用来做其他逻辑，比如用来判断一个库是主库还是备库。因此，修改global变量的方式影响面更大，我不建议你使用。
            - 二是，在异常处理机制上有差异。如果执行FTWRL命令之后由于客户端发生异常断开，那么MySQL会自动释放这个全局锁，整个库回到可以正常更新的状态。而将整个库设置为readonly之后，如果客户端发生异常，则数据库就会一直保持readonly状态，这样会导致整个库长时间处于不可写状态，风险较高。
    - [表锁](<表锁.md>)
        - 表级别的锁有两种
            - 表锁
            - [元数据锁](<元数据锁.md>)（meta data lock，MDL)
    - [行锁](<行锁.md>)
- 日志模块
    - [redolog](<redolog.md>)
        - 当有一条记录需要更新的时候， [InnoDB](<InnoDB.md>)引擎就会先把记录写到[redolog](<redolog.md>) 里面， 并更新内存， 这个时候更新就算完成了。 同时， [InnoDB](<InnoDB.md>)引擎会在适当的时候， 将这个操作
        - 当有一条记录需要更新的时候， [InnoDB](<InnoDB.md>)引擎就会先把记录写到redo log 里面， 并更新内存， 这个时候更新就算完成了。 同时， [InnoDB](<InnoDB.md>)引擎会在适当的时候,将这个操作记录更新到磁盘里面， 而这个更新往往是在系统比较空闲的时候做[InnoDB](<InnoDB.md>)引擎特有的日志面， 并更新内存， 这个时候更新就算完成了。 同时， [InnoDB](<InnoDB.md>)引擎会在适当的时候， 将这个操作记录更新到磁盘里面， 而这个更新往往是在系统比较空闲的时候做
        - 面， 并更新内存， 这个时候更新就算完成了。 同时， [InnoDB](<InnoDB.md>)引擎会在适当的时候， 将这个操作记录更新到磁盘里面， 而这个更新往往是在系统比较空闲的时候做
    - [binlog](<binlog.md>)
        - Server层也有自己的日志， 称为binlog（归档日志）,所有的存储引擎都可以使用
- MySQL逻辑架构
    - server层
        - [连接器](<连接器.md>)
        - [查询缓存](<查询缓存.md>)
            - MySQL8.0取消查询缓存功能
        - 分析器
        - 优化器
        - 执行器
- 使用explain
    - type：查询使用了何种类型，它在 SQL优化中是一个非常重要的指标，以下性能从好到坏依次是：system > const > eq_ref > ref > ref_or_null > index_merge > unique_subquery > index_subquery > range > index > ALL
- [分库分表](<分库分表.md>)

# Backlinks
## [August 4th, 2020](<August 4th, 2020.md>)
- [MySQL](<MySQL.md>)之

- [MySQL](<MySQL.md>)仅

## [CV](<CV.md>)
- ## [MySQL](<MySQL.md>)

## [July 10th, 2020](<July 10th, 2020.md>)
- [MySQL](<MySQL.md>)

## [July 7th, 2020](<July 7th, 2020.md>)
- [MySQL](<MySQL.md>)

