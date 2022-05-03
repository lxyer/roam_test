- 防[[重放攻击]]
    - ![](https://firebasestorage.googleapis.com/v0/b/firescript-577a2.appspot.com/o/imgs%2Fapp%2Flxyer%2Fm6IVvEiVKb.png?alt=media&token=dbd57a31-418b-48a4-9277-b9ffbefc3768)
    - API网关具备防重放能力，有效防范了数据在网络上被窃取后篡改重放的攻击手段。
        - 客户端在请求网关的时候，增加一个X-Ca-Nonce header，值为任意一个字符串。网关会校验15分钟内是否有相同的X-Ca-Nonce传入，如果有，认为是重放请求，网关会立即报错。
        - 此处用的是一个分布式缓存实现，每次请求都校验是否有相同的X-Ca-Nonce。
        - 因为Nonce是参与签名的，所以不能被恶意篡改。
    - [API接口防止参数篡改和重放攻击](https://www.toutiao.com/a6825895919972516365/) #[[Roam-Highlights]]
        - [[API重放攻击]]: 就是把之前窃听到的数据原封不动的重新发送给接收方
        - [[timestamp]]的作用
            - 后台验证时间戳与当前时间之差,超过预定数值未时间失效,缺点无法防止规定时间内的攻击
        - nonce的作用
            - 随机字符串后台收到后放入到[[Redis]]中,如果发现[[Redis]]中已有值,则判断此次访问无效,可以保证参数访问的一次性有效性.缺点为这个[[Redis]]中的数据会越来越大.
        - [[最佳实践]]结合[[timestamp]]和nonce,[[Redis]]中的数据加入过期设置
    - https://www.toutiao.com/a6825895919972516365/
    - 二、问题思考： 重放&DDoS攻击
        - 重放攻击是一种黑客常用的攻击手段, 又称重播攻击、回放攻击, 是指攻击者发送目的主机已接收过的数据, 以达到欺骗系统的目的, 主要用于身份认证过程, 破坏认证的正确性.
        - 如何防御重放攻击
            - 加随机数: 该方法优点是认证双方不需要时间同步，双方记住使用过的随机数, 如发现报文中有以前使用过的随机数, 就认为是重放攻击. 缺点是需要额外保存使用过的随机数, 若记录的时间段较长, 则保存和查询的开销较大.
            - 加时间戳: 该方法优点是不用额外保存其他信息. 缺点是认证双方需要准确的时间同步, 同步越好, 受攻击的可能性就越小. 但当系统很庞大, 跨越的区域较广时, 要做到精确的时间同步并不是很容易.
            - 加流水号: 就是双方在报文中添加一个逐步递增的整数, 只要接收到一个不连续的流水号报文(太大或太小), 就认定有重放威胁. 该方法优点是不需要时间同步, 保存的信息量比随机数方式小. 缺点是一旦攻击者对报文解密成功, 就可以获得流水号, 从而每次将流水号递增欺骗认证端.
        - 阿里云api 使用单次请求签名的方式
    - 方案 1
        - 请求
            - 请求中添加timestamp和nonce值，通过sign签名来判断是否参数是否正常
            - timestamp，nonce，sign都是为了签名和防重放使用。
            - timestamp是发送接口的时间，nonce是随机串，sign是对timestamp,nonce(对于一些rest风格的api，我建议也把url放入sign签名)。签名的方法可以是md5({盐值}key1=val1&key2=val2&key3=val3...)
        - 网关接到这个请求：
            - 1 先验证sign签名是否合理，证明请求参数没有被中途篡改
            - 2 再验证timestamp是否过期，证明请求是在最近时间阈值被发出的
            - 3 最后验证nonce是否已经有了，证明这个请求不是时间阈值内的重放请求
    - 方案 2
        - 配置应用秘钥
            - 业务侧使用秘钥加密
                - timestamp
                - nonce值
                - 证书信息
                - 参数
            - header
                - 证书信息
        - 网关
            - 使用 header 中秘钥进行解密，证明请求参数没有被中途篡改
            - 再验证timestamp是否过期，证明请求是在最近时间阈值内被发出的
            - 最后验证nonce是否已经有了，证明这个请求不是时间阈值内的重放请求