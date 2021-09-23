- Raft将系统中的角色分为领导者（Leader）、跟从者（Follower）和候选人（Candidate）：
    - Leader：接受客户端请求，并向Follower同步请求日志，当日志同步到大多数节点上后告诉Follower提交日志。
    - Follower：接受并持久化Leader同步的日志，在Leader告之日志可以提交之后，提交日志。
    - Candidate：Leader选举过程中的临时角色。
- Raft要求系统在任意时刻最多只有一个Leader，正常工作期间只有Leader和Followers。
- Leader选举
    - Raft 使用心跳（heartbeat）触发Leader选举。当服务器启动时，初始化为Follower。Leader向所有Followers周期性发送heartbeat。如果Follower在选举超时时间内没有收到Leader的heartbeat，就会等待一段随机的时间后发起一次Leader选举。
    - 赢得了多数的选票，成功选举为Leader；
    - 收到了Leader的消息，表示有其它服务器已经抢先当选了Leader；
    - 没有服务器赢得多数的选票，Leader选举失败，等待选举时间超时后发起下一次选举。
    - 选举出Leader后，Leader通过定期向所有Followers发送心跳信息维持其统治。若Follower一段时间未收到Leader的心跳则认为Leader可能已经挂了，再次发起Leader选举过程。

# Backlinks
## [微服务](<微服务.md>)
- [Raft算法](<Raft算法.md>)

