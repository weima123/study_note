## 一.rabbitMq安装
```
https://www.cnblogs.com/yihuihui/p/9095130.html
```

## 二.基本概念
### 1.mq的概念:在消息的传输过程中，保存消息的容器

### 2.mq的使用场景
```
https://www.jianshu.com/p/7af92d08ceca
1) 异步场景
2）应用解耦
3）流量削峰
```

### 3.rabbitMq基本概念
```
1)producer:消息的生产者
2)consumer:消息的消费者
3）exchange:交换器，决定消息照着什么规则，路由到哪个队列
4）queue:队列，消息的实际载体
5）binding:绑定，将交换器和队列进行绑定
6)routingKey:路由关键字，exchange根据这个关键字进行消息的投递

```

### 4.rabbitmq的实现：hello-word

### 5.rabbitmq 队列消息分发的两种模式
```
1)轮询分发：
2)公平分发：
```

### 6.消息应达、持久化
```
1）exchange 、queue支持持久化
2）消息发送成功ack
3) 消息消费成功ack
```

### 7.rabbitMQ的交换机模式
#### 1)fanout 发布订阅模式：
```
1）多个queue绑定到同一个exchange上，
2）一份消息被发送到多个queue上
3)没有routing_key的概念
```
#### 2)direct模式：
````
1)queue绑定directExchange的同时指定bindingKey（routing-key）
2)只有消息的routing-key和交换机的bindingKey一致的情况下消息才能被投递到队列
````
#### 3)topic模式：
```
1)queue绑定topicExchange的同时指定bindingKey(routing-key)
2)只有消息的routing-key和交换机的bindingKey一致的情况下消息才能被投递到队列
3）# * 的区别：#匹配多个单词，*匹配一个单词，
 3.1)del.#.goods 能匹配del.one.goods 能匹配del.one.eating.goods
 3.2)del.*.goods 只能匹配del.one.goods 不能匹配del.one.eating.goods
```
#### 4)header模式
```

```

### 8.rabbitmq常见问题
#### 1.生产者推送消息到mq过程，如何保证消息推送成功？
```
1）消息成功传到队列后，mq通过ack消息返回结果
2）rabbitmq事物
```

#### 2.mq推送消息给消费者，如何保证消息消费成功？
```
消息消费成功后消费者手动ack通知mq
```
#### 3.消费者消费消息，如何保证消息的幂等性？

