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
    - 2.Broker 存储阶段
        - 默认情况下，消息只要到了 Broker 端，将会优先保存到内存中，然后立刻返回确认响应给生产者。随后 Broker 定期批量的将一组消息从内存异步刷入磁盘。
        - 保证 Broker 端不丢消息，保证消息的可靠性，我们需要将消息保存机制修改为同步刷盘方式，即消息**存储磁盘成功**，才会返回响应。
        - **集群部署**
            - 为了保证可用性，Broker 通常采用一主（**master**）多从（**slave**）部署方式。为了保证消息不丢失，消息还需要复制到 slave 节点。
            - 默认方式下，消息写入 **master** 成功，就可以返回确认响应给生产者，接着消息将会异步复制到 **slave** 节点。
