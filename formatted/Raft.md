- 初次选举
    - 在算法刚开始时，所有结点都是 Follower，每个结点都会有一个定时器，每次收到来自 Leader 的信息就会更新该定时器。
    - 如果定时器超时，说明一段时间内没有收到 Leader 的消息，那么就可以认为 Leader 已死或者不存在，那么该结点就会转变成 Candidate，意思为准备竞争成为 Leader。
    - 如果 Candidate 在一定时间内没有获得足够的投票，那么就会进行一轮新的选举，直到其成为 Leader,或者其他结点成为了新的 Leader，自己变成 Follower。
    - 成为 Candidate 后结点会向所有其他结点发送请求投票的请求（RequestVote），其他结点在收到请求后会判断是否可以投给他并返回结果。Candidate 如果收到了半数以上的投票就可以成为 Leader，成为之后会立即并在任期内定期发送一个心跳信息通知其他所有结点新的 Leader 信息，并用来重置定时器，避免其他结点再次成为 Candidate。
- 再次选举
    - 再次选举会在两种情况下发生
        - Leader 下线，此时所有其他结点的计时器不会被重置，直到一个结点成为了 Candidate，和上述一样开始一轮新的选举选出一个新的 Leader。
        - Follower 结点与 Leader 间通信发生问题，导致发生了分区，这时没有 Leader 的那个分区就会进行一次选举

# Backlinks
## [CV](<CV.md>)
- 哨兵认为master客观下线后，故障恢复的操作需要由选举的领头哨兵来执行，选举采用[Raft](<Raft.md>)算

## [Nacos](<Nacos.md>)
- [Raft](<Raft.md>)(
