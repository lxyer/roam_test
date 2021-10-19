- **Nacos中的协议**
    - Distro(在Nacos中为了实现[AP](<AP.md>)，自己定制了一套[Distro](<Distro.md>)协议。)
        - 在Nacos的Instance(实例)中提供了一个ephemeral字段，这个字段是bool类型，这个字段和ZK中的含义差不多，都代表的是否是临时节点，在Nacos中如果是临时节点就会使用[AP](<AP.md>)协议，如果不是临时节点就会走[CP](<CP.md>)。当然在注册中心中所有的实例其实默认都是临时节点。
        - 在Distro中所有的数据都是用内存进行保存,Distro是用ConcurrentHashMap作为存储的容器，不需要使用额外的文件进行存储。
    - [Raft](<Raft.md>)(实现了CP)
    - [Nacos配置中心数据同步原理](https://blog.csdn.net/m0_54065725/article/details/113075357)
    - 

# Backlinks
## [微服务](<微服务.md>)
- [Nacos](<Nacos.md>)

