- 不丢失消息
    - 消息的发送流程
        - ![](https://firebasestorage.googleapis.com/v0/b/firescript-577a2.appspot.com/o/imgs%2Fapp%2Flxyer%2FkkeWwzi-9P.png?alt=media&token=09eb9319-aefa-47bb-b5d0-aa266b8e142e)
    - 1.生产阶段不丢失
        - 同步
            - send 方法是一个同步操作，只要这个方法不抛出任何异常，就代表消息已经**发送成功**
        - 异步（异步的发送的方式，适合于链路耗时较长，对响应时间较为敏感的业务场景）
            - 异步发送消息一定要**注意重写**回调方法SendCallback()，在回调方法中检查发送结果
        - 不管是同步还是异步的方式，都会碰到网络问题导致发送失败的情况。针对这种情况，我们可以设置合理的重试次数，当出现网络问题，可以自动重试。设置方式如下：
        - ```javascript
// 同步发送消息重试次数，默认为 2
mqProducer.setRetryTimesWhenSendFailed(3);
// 异步发送消息重试次数，默认为 2
mqProducer.setRetryTimesWhenSendAsyncFailed(3);```
        - 不管是同步还是异步的方式，都会碰到网络问题导致发送失败的情况。针对这种情况，我们可以设置合理的重试次数，当出现网络问题，可以自动重试。
    - 2.Broker 存储阶段
        - 默认情况下，消息只要到了 Broker 端，将会优先保存到内存中，然后立刻返回确认响应给生产者。随后 Broker 定期批量的将一组消息从内存异步刷入磁盘。
        - 保证 Broker 端不丢消息，保证消息的可靠性，我们需要将消息保存机制修改为同步刷盘方式，即消息**存储磁盘成功**，才会返回响应。
        - **集群部署**
            - 为了保证可用性，Broker 通常采用一主（**master**）多从（**slave**）部署方式。为了保证消息不丢失，消息还需要复制到 slave 节点。
            - 默认方式下，消息写入 **master** 成功，就可以返回确认响应给生产者，接着消息将会异步复制到 **slave** 节点。
    - 3. 消费阶段
        - 消费者从 broker 拉取消息，然后执行相应的业务逻辑。一旦执行成功，将会返回 ConsumeConcurrentlyStatus.CONSUME_SUCCESS 状态给 Broker。否则我们需要返回 ConsumeConcurrentlyStatus.RECONSUME_LATER，稍后再重试。
    - 最重要的是消费客户端，需要注意保证**幂等性**。
- 消息零丢失方案总结
    - 生产者使用同步发送，或者发送事务消息
    - Broker配置同步刷盘 + 同步复制
    - 消费者不要使用异步消费
    - 整个MQ挂了之后准备降级方案
- 如何处理大量积压的消息？
    - ①先修复consumer的问题，确保其恢复消费速度，然后将现有consumer都停掉。
    - ②临时建立好原先10倍或者20倍的queue数量(新建一个topic，partition是原来的10倍)。
    - ③然后写一个临时分发消息的consumer程序，这个程序部署上去消费积压的消息，消费之后不做耗时处理，直接均匀轮询写入临时建好分10数量的queue里面。
    - ④紧接着征用10倍的机器来部署consumer，每一批consumer消费一个临时queue的消息。
    - ⑤这种做法相当于临时将queue资源和consumer资源扩大10倍，以正常速度的10倍来消费消息。
    - ⑥等快速消费完了之后，恢复原来的部署架构，重新用原来的consumer机器来消费消息。
- 如何保证顺序
    - RocketMQ是支持顺序消费的。但这个顺序，不是全局顺序，只是分区顺序。
    - 生产者在发布消息的时候重写selector，按照规则

# Backlinks
## [MQ](<MQ.md>)
- [RocketMQ](<RocketMQ.md>)

