订阅，取消订阅和发布实现了发布/订阅消息范式(引自wikipedia)，发送者（发布者）不是计划发送消息给特定的接收者（订阅者）。而是发布的消息分到不同的频道，不需要知道什么样的订阅者订阅。订阅者对一个或多个频道感兴趣，只需接收感兴趣的消息，不需要知道什么样的发布者发布的。这种发布者和订阅者的解耦合可以带来更大的扩展性和更加动态的网络拓扑。

为了订阅foo和bar，客户端发出一个订阅的频道名称:

```
SUBSCRIBE foo bar
```
其他客户端发到这些频道的消息将会被推送到所有订阅的客户端。

客户端订阅到一个或多个频道不必发出命令，尽管他能订阅和取消订阅其他频道。订阅和取消订阅的响应被封装在发送的消息中，以便客户端只需要读一个连续的消息流，其中第一个元素表示消息类型。

## 推送消息的格式
消息是一个有三个元素的多块响应 。

第一个元素是消息类型:

subscribe: 表示我们成功订阅到响应的第二个元素提供的频道。第三个参数代表我们现在订阅的频道的数量。

unsubscribe:表示我们成功取消订阅到响应的第二个元素提供的频道。第三个参数代表我们目前订阅的频道的数量。当最后一个参数是0的时候，我们不再订阅到任何频道。当我们在Pub/Sub以外状态，客户端可以发出任何redis命令。

message: 这是另外一个客户端发出的发布命令的结果。第二个元素是来源频道的名称，第三个参数是实际消息的内容。

## 数据库与作用域

发布/订阅与key所在空间没有关系，它不会受任何级别的干扰，包括不同数据库编码。 发布在db 10,订阅可以在db 1。 如果你需要区分某些频道，可以通过在频道名称前面加上所在环境的名称（例如：测试环境，演示环境，线上环境等）。

## 同时匹配模式和频道订阅的消息
客户端可能多次接收一个消息，如果它订阅的多个模式匹配了同一个发布的消息，或者它订阅的模式和频道同时匹配到一个消息。就像下面的例子：
```
SUBSCRIBE foo
PSUBSCRIBE f*
```
上面的例子中，**如果一个消息被发送到foo,客户端会接收到两条消息：一条message类型，一条pmessage类型。**

